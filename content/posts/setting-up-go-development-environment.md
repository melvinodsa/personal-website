---
title: "Setting up Go development environment"
date: 2018-11-08T15:17:22+05:30
---

> ![alt text](/2/1.webp "vim setup for go")
> My current Development IDE - Vim

> Clear is better than clever

The following topics will be covered in this post

- Overview of _#Go_
- Installing Go
- Setting up workspace
- Setting up an IDE

# Prerequisite

Well, there isn't much. If can follow this post, it would be sufficient.

# Overview of Go

Go is a general purpose programming language. It is used by engineers from different domains to create a lot of awesome stuffs. Be it a simple IoT application or a large scale B2C platform, _#golang_ should fit in. It's designed by engineers working in Google and
is open source. A lot of companies that have products that needs to be scaled are betting on Go like Uber, Google etc. One of the main advantage of go is that the language has got a small grammar, hence you can jump into it within a couple of hours. It's designed to run in cloud and you would simply love it once start using it. There are exceptions where you simply don't want to build in Go. Reasons could be your organisational constraints, functional programming background etc. But the point is, there is nothing to lose trying out the language. It opens up a wide range of possibilities by easing up difficult concepts like multi-threaded programming, memory management, OOPs etc. There are still areas where go as a community should come together to have healthy discussions like error handling, modularisation, generics etc. Go enforces code readability, dev practices etc. which turns out to be win-win situation for a developer at the end of the day. Last but not least, tools. The amount tools available in the ecosystem awards a better coding experience through out the development life cycle.

# Installing Go

Installing Go is pretty straight forward. There is a general trend between linux users to do an apt-get install of any software. I don't generally prefer this method, because maintaining multiple versions or upgrading the language become a pain afterwards. I would suggest using the installation method mentioned in the official [website](https://golang.org/doc/install)

## Linux/macOS

Users can download the archive directly from their [website](https://golang.org/dl/). Once the archive is downloaded, one can extract tit directly into /usr/local by the following command

```sh
tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz
```

where go$VERSION.$OS-\$ARCH.tar.gz is the name of the archive file you've just downloaded. For example go1.11.1.linux-amd64.tar.gz would be the name of the archive where downloaded version is 1.11.1 and the operating system is linux 64 bit.

## Windows

Windows users has got two options for installing Go.

1. From zip file - This method is similar to the one we had in linux/macOS. Download the [archive](https://golang.org/dl/) and unzip it and place them in the folder c:\Go

2. Using MSI installer - This is pretty much straight forward. Just download the [MSI installer](https://golang.org/dl/) and double click the installer. It will take care of the rest.

Once installed you can check the installation by typing the following command in command line/terminal

```sh
go version
# go version go1.11.1 darwin/amd64
```

We are good to go here. In the next section we will see how to setup the workspace.

# Setting up workspace

## Prerequsite

[git](https://git-scm.com/) / [mercurial](https://www.mercurial-scm.org/)

Why we need a workspace. It's just plain simple. To organise the code and the projects, I would prefer a single place. Cause searching for the project files in the folder mess that I have created is the last thing I want to. Also it is easy to get the fetch the projects directly from github or from your private repositories using go get. Usually go workspace is created in a folder called go in your home directory if you've used any installers(mac/windows). If it is not created, you can create one. Usually I create a workspace directory in my home folder. I keep both my go projects and other coding projects in this workspace itself. Workspace should have two directories called **src** and **bin**.

> ![alt text](/2/2.webp "default go workspace structure")
> Workspace directory structure

Usually go keeps all the binaries that have been created using go install in bin and your source code in src. If you're from the Java background, you'd know the pain of using libraries without knowing the source code or why the library function is not behaving the way you'd expect it to do. Well with go you can say farewell to all those problems. With go you can go to source code of any library and even to logs in it and happy debugging. If you do a go get

```sh
 go get golang.org/x/tools/cmd/godoc
```

a directory structure will be created in the src directory like this :- golang.org/x/tools/cmd/godoc where you can find the source code of godoc project.
pkg directory will have the compiled source code in it.

> One thing about `go get` is that I use it instead of _#git_; don't just clone, use go get instead.

One last thine. include the workspace and bin directory location in your environment variable.

For macOS/linux user add the following line into your .bash_profile ->

```bash
export GOPATH=$HOME/workspace
export PATH="$PATH:$GOPATH/bin"
```

If you installed the go using a package installer probably your workspace directory name would be **_go_** instead of **_workspace_**.

For windows users go to Start Menu search for environment variables. Select the one for adding environment variables in your account. Create an environment named GOPATH with the full path location of your workspace. Edit the path variable, if not existing create one - called path with value as the path to the bin directory in the go workspace. If the variable is existing add the path like this **_other_paths;C:\Users\melvin\go\bin_**

If you need to access private repositories and have the ssh keys added to the repository manager like github/bitbucket (follow [this link](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)) add a following gitconfig file to your home directory with the following contents

```git
[url "git@bitbucket.org:"]
    insteadOf = https://bitbucket.org/
[user]
    name = your_git_username
    email = youemailid@abcd.org
```

# Setting up an IDE

If you're new to programming I would recommend you to use VS Code. You can easily install it by downloading it from the vs code [website](https://code.visualstudio.com/). Install the Go plugin for VS Code. Your IDE will take care of the rest by suggesting and asking for your permission to install the required go tools. But if you're a pro dev, you know the way _#vim_. Use my [vimrc](https://gist.github.com/melvinodsa/d526191883601fbdecbf3be9baf14a77). My IDE looks like the one that is at the top of this post.
