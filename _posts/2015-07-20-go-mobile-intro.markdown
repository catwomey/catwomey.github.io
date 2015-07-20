---
layout: post
title:  "See Android Go! Go, Android, Go!"
date:   2015-07-30
tags: go mobile 
---

With the upcoming release of go 1.5 a spectacular new feature is being made available.  The ability to compile go code to work on android or ios.  This is great for a number of reasons, but what I love about it is now you can put common code (such as business logic) into a common spot and share for both platforms.  Google has even done some of the legwork to make sure that apps written in go aren't rejected from the app store with [Ivy](https://itunes.apple.com/us/app/ivy-big-number-calculator/id1012116478?mt=8). 

##Getting started

So how do you get started with this wonderful new development?  Well first you need to have a working go 1.5 install.  You can compile it from [source](https://golang.org/doc/install/source) or [pre compiled](https://golang.org/dl/).  Once you have a working go install you need to install the gomobile command by running:

```
 go get golang.org/x/mobile/cmd/gomobile
 gomobile init

```

For Android: you need to install the android [sdk](https://developer.android.com/sdk/installing/index.html?pkg=tools) and ensure that adb is on your path and that usb debugging is enabled on your device.

For IOS: Unfortunately IOS support isn't 100% ready for prime time and may not work.  However to compile for IOS you do still need to have xcode installed and be working on OSX. 


##Installing an example

Before we get to writing code, let's make sure we can compile some example code. Google has provided some [examples](https://godoc.org/golang.org/x/mobile/example) we can use. 

Assuming you installed everything correctly the following commands should compile and install some sample applications"

```
 gomobile install golang.org/x/mobile/example/basic
 gomobile install golang.org/x/mobile/example/audio
 gomobile install golang.org/x/mobile/example/sprite
```

Might not seem like much, but I thought it was pretty cool. The above applications are pure go and are using opengl for display.  Currently the apis are somewhat restricted but I expect this will improve as go matures.

##Anatomy of cross platform apps

Ok so we can compile other people's code, but how do we create an app for ourselves?  Let's start with the classic hello world app.  

https://godoc.org/golang.org/x/mobile/app use this package to write go apps useable on both platforms.  caveate limited to apis on all platforms


Writing cross platform libraries


https://godoc.org/golang.org/x/mobile/cmd/gobind
