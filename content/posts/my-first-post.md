---
title: "Setting up mysql-server docker container on mac os"
date: 2018-10-18T15:17:22+05:30
---

> ![alt text](/1/1.webp "Sucessfully setup docker")
> Finally I did it

I was starting the backend sever development of a much anticipated project. So created the basic auth. Now I need to store the user info. Let's use **_#mysql_**.

```sh
brew install mysql
```

Wait.. I just shouldn't install mysql in macOS. Why? Well the good old problem. I write the code and ships it. Suddenly something goes off. **But it works in my system**. Ooh I use macOS, but prod system is linux. **So _*#Docker*_ is the way to go.** Well I was very much enthusiastic about it. Directly navigated to the docker website, logged in and downloaded the docker for mac. Installed it. Well everything goes right. Now I pulled the mysql server docker image and ran it.

```sh
docker run -d --name app-mysql -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql/mysql-server
docker ps
```

Bhoom! docker app-mysql is up and healthy.. Now let me login to mysql

```sh
mysql -uroot -p
```

> ![alt text](/1/2.webp "First error")
> Oops let's Googe it

Well... something is off. What is it? mysql running in docker is not accessible in through localhost. I was new to macOS. I am linux since college. Then what is my ip address that can be used. ifconfig in linux works well. Here I am trapped. Did some googling. So first I need to get the interface through which I can connect(I was hoping docker connects through docker0, Well thanks to macOS, there is no interface like that). **networksetup** is the goto man for us. I ran the command

```sh
networksetup -listallhardwareports
```

> ![alt text](/1/3.webp "Netowrk")
> listing all the networks

So my interface name is en1, since I was connected to my wifi. Now I run the command to get the ip address

```sh
ipconfig getifaddr en1
#192.168.43.180
```

Ok cool, now I got it sorted out.
Again ran the command

```sh
mysql -uroot -h192.168.43.180 -p
```

Now I get the error
`ERROR 1130 (HY000): Host '172.17.0.1' is not allowed to connect to this MySQL server`

So I have to enable my macOS host machine to connect to mysql running in the docker container from the ip 172.17.0.1. For starters we have a network interface where mysql is allotted an ip 172.17.0.2 and host machine 172.17.0.1. Hmm... I will create a root user from 172.17.0.1. I think that would sort it out. I logged into the docker container mysql

```sh
docker exec -it app-mysql mysql -uroot -p
```

Inside mysql shell I gave the commands to do the same

```
CREATE USER 'root'@'172.17.0.1' IDENTIFIED BY 'root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'172.17.0.1';
FLUSH PRIVILEGES;
```

I verified it

> ![alt text](/1/4.webp "mysql user setup verified")
> mysql user from 127.17.0.1

And finally ran the command

```sh
mysql -uroot -h192.168.43.180 -p
```

and got the image shown at the top of this post.
