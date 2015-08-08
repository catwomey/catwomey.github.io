---
layout: post
title:  "Go libraries for iOS and Android"
date:   2015-08-08
tags: go mobile 
---

In my last post I introduced you to gomobile and how you can build a go/opengl application for deployment on iOS and Android. Today I will show you how easy it is to create a cross platform library based in go.  For the examples in this post I am assuming you have a working go 1.5 environment as defined in my [previous post]({{page.previous.url}}).

##Why go?

It's fairly likely that if you are creating any non-trivial app you are already using libraries to supplement your code.  If you are targeting multiple platforms it's likely that you've had to find/write libraries for both platforms, so why not bring it down to 1 library written in go? Potential library use cases I can think of are REST clients, and business logic code.

Personally I find that writing go code is more enjoyable and less verbose than writing Java for Android. I don't have much exposure to objective-c however.

##Show me how

First let's start with a really simple go program. I've written a [sudo random number generator](https://github.com/ctwomey1/CrossPlatformLib) seeded with the current second using the default golang libraries.

```go
package random

import (
	"math/rand"
	"time"
)

func SudoRandomNumer() int {
	return rand.Intn(time.Now().Second())
}
```

Now I can run gomobile bind to generate an Android Archive (.aar) file, to generate for iOS you must include -target iOS. To include the .aar file in an Android project using Android Studio use the module import wizard (File > New > New Module > Import .JAR or .AAR package), and setting it as a new dependency (File > Project Structure > Dependencies).  Alternatively you can generate the Gradle configuration yourself.


Now create a program to use the go library. I've created a very simple [Android project](https://github.com/ctwomey1/HelloWorldGoLib) that displays the random number on create. The main activity using our go code is:

```Java
package com.codingvelocity.helloworldgolib;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.widget.TextView;
import go.random.Random;
public class HelloActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_hello);
        TextView view = (TextView) findViewById(R.id.random);
        view.setText(String.valueOf(Random.SudoRandomNumer()));
    }

    //boiler plate code
}
```

##How does it work?

For Android the gomobile command generates [jni bindings](http://developer.android.com/training/articles/perf-jni.html) and a shared object file (.so) so the two can interact.  For iOS gomobile generates a shared archive file (.a) that can be imported in objective c code. 

##Gotchas

Currently the exported function return types are limited to a [small number of types](https://godoc.org/golang.org/x/mobile/cmd/gobind#hdr-Type_restrictions) but should be expanded on in the future.

iOS support is still a work in progress, so in reality this isn't quite ready for prime time.



###Further reading
[https://godoc.org/golang.org/x/mobile/cmd/gobind](https://godoc.org/golang.org/x/mobile/cmd/gobind)