---
layout: post
title:  "See Android Go! Go, Android. Go!"
date:   2015-07-23
tags: go mobile
---

Using the [gomobile tool chain](https://godoc.org/golang.org/x/mobile) and go 1.5 you can compile go code to work on Android and iOS. You can either write your application completely in go with opengl for your ui, or write a go library that can interact with native android/iOS. This opens up the doors for cross platform libraries (business logic anyone?), which has me pretty excited.  Google has even done some of the legwork to make sure that apps written in go aren't rejected from the app store with [Ivy](https://itunes.apple.com/us/app/ivy-big-number-calculator/id1012116478?mt=8).

In this post I will go over the very basic setup to compile and install a go app in Android, and briefly explain what's going on.

## Getting started

Let's get started, first you need to have a working go 1.5 install.  You can compile it from [source](https://golang.org/doc/install/source) or use the [pre compiled version](https://golang.org/dl/).  I used go 1.5 beta2 while writing this blog post on a Linux machine.  I noticed some warnings in the go mobile source about things not working in Windows so if you are using Windows for development this may not work.

Once you have a working go install, you need to install the gomobile command by running:

```
 go get golang.org/x/mobile/cmd/gomobile
 gomobile init

```

For Android: you need to install the Android [sdk](https://developer.android.com/sdk/installing/index.html?pkg=tools) and ensure that adb is on your path and that usb debugging is enabled on your device.

For iOS: Unfortunately iOS support isn't 100% ready for prime time and may not work.  As usual to compile for iOS you do still need to have xcode installed and be working on OSX.


## Installing an example

Let's see if we can compile and install some go code. Google has provided some [examples](https://godoc.org/golang.org/x/mobile/example) we can use. For the sake of simplicity I'm only going to talk about installing to android from here. (mostly because I don't have an iOS device to try)

The following commands will install the sample applications

```
 gomobile install golang.org/x/mobile/example/basic
 gomobile install golang.org/x/mobile/example/audio
 gomobile install golang.org/x/mobile/example/sprite
```

Might not seem like much, but I thought it was pretty cool. The above applications are pure go and are using opengl for display.  Currently the apis are somewhat restricted but I expect this will improve as go matures.

## Anatomy of cross platform apps

OK so we can compile other people's code, but what is it doing? Let's take a look at what's happening in the basic example.

``` go
//excerpt from golang.org/x/mobile/example/basic
func main() {
	app.Main(func(a app.App) {
		var c config.Event
		for e := range a.Events() {
			switch e := app.Filter(e).(type) {
			case lifecycle.Event:
				switch e.Crosses(lifecycle.StageVisible) {
				case lifecycle.CrossOn:
					onStart()
				case lifecycle.CrossOff:
					onStop()
				}
			case config.Event:
				c = e
				touchLoc = geom.Point{c.Width / 2, c.Height / 2}
			case paint.Event:
				onPaint(c)
				a.EndPaint()
			case touch.Event:
				touchLoc = e.Loc
			}
		}
	})
}
```

Apps written in go are expected to call the Main function from the app package.  From there you can define what certain events should do, check the [event documentation](https://godoc.org/golang.org/x/mobile/event) for in depth details.  These events fire based on registered event interface{} in the app.

The above code is looping through all events that come in on the event channel. The config event defines the size of the screen, the paint event is cycling the colour of our triangle.  The touch event changes the position of the triangle, and the lifecycle event constructs or destructs the program based on application focus.

## Further reading

I hope I have piqued your interest, to take go mobile further check out these links

All the examples can be found [http://golang.org/x/mobile/example](http://golang.org/x/mobile/example) <br/>
Documentation can be found [https://godoc.org/golang.org/x/mobile](https://godoc.org/golang.org/x/mobile) <br/>
Source can be found [https://github.com/golang/mobile](https://github.com/golang/mobile)
