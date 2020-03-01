---
title: "Building a high performant concurrent cache in Golang"
date: 2019-04-09T15:17:22+05:30
---

I have met many folks who are confused with the use-case for Go concurrency and channels. In this blog post, I explain how to create a simple cache using the concept of concurrency and channels. Building a memory safe cache is a pain. Often developers directly go for techs like Redis and other in-memory caches for the same. These in-memory caches are all excellent and well testing no doubt in that. However, sometimes it turns out that you didn't require such a complex system. At the end of this post, I compare the performance of the cache we built-in with Redis.

# Let's begin

The closest feature to in-memory cache in Go is the map. So when we need to build a high performant cache that shouldn't choke under traffic. When we talk about heavy traffic, the only area where people become skeptical safety in using it in a concurrent environment, as per Go https://golang.org/doc/go1.8#runtime, they could get abused if used in a concurrent environment. If you read and write into a map in different goroutines, under heavy traffic, your program might end up crashing.

# Go Routines and Channels

What's the most straightforward hack? Do read and write in a single goroutine itself. Let your cache be a goroutine that lives in the entire life cycle of the application. One can interact with the cache using Go Channels with the help of Request. The request is a struct that holds the key, payload, type of operation and an output channel to get back the response.

```go
//Type holds the request type of operation like read and write
type Type uint

const (
	//READ from the cache
	//if the key doesn't exist in the cache it will return nil
	READ Type = iota
	//WRITE into the cache
	WRITE
	//DELETE keys from the cache
	DELETE
)

//Request facilitates the read and write to the cache go routine
type Request struct {
	//Type is the type of the request
	Type Type
	//Payload to be written inot the cache the operation is write
	Payload interface{}
	//Key is the key with the payload to be accessed or written into
	Key string
	//Out chan is for getting the response in case of read operation
	Out chan Request
}
```

All the interaction to the cache happens through a channel of Request type to the cache.
At the startup, the cache goroutine comes into life. Then go into an infinite for loop waiting for requests to show up based on the type of operation, the Request intent to perform, cache complete the Request. The below image has the code implementation for the same.

```go
//RequestChannel through which the requests can be made
var RequestChannel = make(chan Request)

func init() {
	go Cache(RequestChannel)
}

//Cache is the in-memory cache go routine
func Cache(ch chan Request) {
	cache := LoadPersistant()
	for {
		req := <-ch
		switch req.Type {
		case READ:
			//Read operation
			req.Payload, _ = cache[req.Key]
			go SendToChannel(req.Out, req)
		case WRITE:
			//Write operation
			cache[req.Key] = req.Payload
		case DELETE:
	;		//Delete operation
			delete(cache, req.Key)
		}
	}
}

//SendToChannel will send a rquest to the channel. It is intended for use along with routines
//If you want regulate the spawning of the go routines, you can use a go routine pool design
func SendToChannel(ch chan Request, req Request) {
	ch <- req
}
```

When the go routine spins up, we load the cache from the persistent storage and then perform update/put/read operations in the cache as per request. Goroutines used for sending the request payloads. One catch is that if too many goroutines spin up, it might be an issue. You could improve by creating a routine pool and serving from the pool to regulate the spawning of goroutines.

Things left for the developer’s creativity:-

- Permissions
- Sharding
- Master/Slave

You can check out the [code](https://github.com/melvinodsa/go-experiments/tree/master/cache)

# Questions

_Q:_ Is using switch a right approach? Shouldn't we be using different channels for write and read operations and select for multiplexing?

_A:_ Depends upon how you want to design the read and write. If the read and write come from different entities, you can go ahead, but again benchmark it. My benchmark results say the approach with switch and a single channel is faster than select. Check out my tweet on the [same](https://twitter.com/melvinodsa/status/1031434710184939522)

>

_Q:_ Is it worth writing this piece of code than just going ahead and use Redis?

_A:_ Again depends, if you need to keep a simple cache and no other fancy stuff like message broker, master/slave. It's is ridiculously faster. Check out the benchmark results.

> ![alt text](/4/1.webp "go cache comparison with Redis")
> Comparison with redis cache
