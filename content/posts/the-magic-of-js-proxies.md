---
title: "The magic of JS Proxies"
date: 2018-11-23T15:17:22+05:30
---

> ![alt text](/3/1.webp "undefined error appearing while using traversing deep nested objects")
> Want to get rid of undefined error

You know the pain of this error. Being a JS developer, I often rely on an awful yet effective method to escape this loop.

```js
var y = x && x.bar ? x.bar.foo : undefined;
```

Yeah I know, when we start evaluating more complex objects it will become pretty nasty; yet I will argue it would never fail. Recently I came across Proxies in JS and found a pretty interesting concept introduced by Erik Vullum during a talk at JSConf Budapest. Proxies are pretty cool that I would recommend it a practice in their daily JS.

# What are proxies?

As per MDN, the Proxy object is used to define custom behavior for fundamental operations on objects. For example, property lookup, assignment, enumeration, function invocation, and several other features can be changed with them. Suppose the number 37 should be returned as the default value when the property name is not in the object. Let's see how.

```js
var handler = {
  get: function(obj, prop) {
    return prop in obj ? obj[prop] : 37;
  }
};

var p = new Proxy({}, handler);
p.a = 1;
p.b = undefined;

console.log(p.a, p.b); // 1, undefined
console.log("c" in p, p.c); // false, 37
```

# Insurance for undefined

First of all, let's make the undefined a well-defined proxy function. We will define an empty JS Proxy function. We will add this proxy to objects which required to be safe.

```js
const Undefined = new Proxy(function() {}, {
  get(target, key, receiver) {
    if (key === "name") {
      return "Undefined";
    }

    return Undefined;
  },
  apply() {
    return Undefined;
  }
});
```

Now we make a function called insurance that will return a proxy for the given object. When an undefined property is accessed, the insurance function will return the function defined above. In all other cases, it works as expected.

```js
function insurance(obj) {
  return new Proxy(obj, {
    get(target, key) {
      const accessedProperty = Reflect.get(target, key);

      if (typeof accessedProperty === "object") {
        return insurance(accessedProperty);
      } else {
        return accessedProperty == undefined ? Undefined : accessedProperty;
      }
    }
  });
}
```

So buckle up, we are going to create an object with insurance function that will never fail.

```js
const iNeverFail = insurance({
  foo: "bar",
  baz: {
    qux: 10,
    name: "yoyo"
  }
});

iNeverFail.asd; //ƒ anonymous()
iNeverFail.asd.name; // "Undefined"
```

At this point, I would love to share the source of inspiration for this post.

{{< youtube _5X2aB_mNp4 >}}
