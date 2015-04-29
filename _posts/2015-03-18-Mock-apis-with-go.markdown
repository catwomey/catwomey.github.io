---
layout: post
title:  "Mocking Web APIs for Coding Velocity"
date:   2015-04-12
categories: golang 
tags: golang mockAPI
---

Creating mock APIs seems to be something that is overlooked by even seasoned developers, but i've personally found this to be
one of the best tools at my disposal.  At the end of this post I hope that i've inspired you to do it on your next project

##What is a mock API?

A mock web API, is simply a standalone program that mimics the behavior of your real web API, but returns hardcoded responses.

##Why should you mock an API?

* Mock APIs can be a replacement for an API that is still in development

    Coding against something that is real and gives you a short feedback loop is invaluable. This gives you coding velocity 
    when you only know the abstract details of your API and not the internals 
* Mock APIs let you have as many dev/test environments as you want

    Sometimes API access is restricted to a finite number of requests or modifies data so actions can't be repeated. A 
    mock API lets you be in control instead.  Additionally a mock API will always respond the way you coded it to, so demos
    to users should always work as expected
* Great opportunity to learn something new

    Regardless of the technology stack you are currently working in, no one will care how you test your application. So 
    use this time to learn something new and explore a new technology. 

##OK you convinced me, now show me!

The simplest form of a mock API (in go) is:

 
 ```go
package main

import (
	"fmt"
	"net/http"
)

func response(w http.ResponseWriter, req *http.Request) {
	fmt.Fprintf(w, "EXPLOSIONS!")
}

func main() {
	http.HandleFunc("/mockAPI", response)
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		fmt.Println(err)
	}
}
 ```
 Pretty easy eh?  Now this example isn't exactly powerful, and chances are the reason you are using an API is it's doing
 some non trivial operation.  But let's step back and examine this code for a second, in a matter of a few minutes we configured 
 an http server that returns any text we want, that's pretty powerful stuff.  
 
 Let's make it more powerful.  Most APIs allow you to send some sort of parameter to identify what you are requesting, so
 let's add one to our mock and have it be responsive to the parameter we send in.  
 
 For the sake of context lets say our API identifies the properties of an object based on the string we send in
 
 
 ```go
 
package main

import (
	"fmt"
	"io/ioutil"
	"log"
	"net/http"
	"os"
	"path/filepath"
)

//gets the current directory of the go executable
func getCurrentDirectory() string {
	dir, err := filepath.Abs(filepath.Dir(os.Args[0]))
	if err != nil {
		log.Println(err)
	}
	return dir
}

func logError(caller string, err error) {
	if err != nil {
		log.Println(caller, err)
	}
}

func response(w http.ResponseWriter, req *http.Request) {
	thing := req.URL.Query().Get("thing")
	file, err := ioutil.ReadFile(getCurrentDirectory() + "/" + thing + ".json")
	logError("response writer", err)
	fmt.Fprintf(w, string(file[:]))
}

func main() {

	http.HandleFunc("/mockAPI", response)

	err := http.ListenAndServe(":8080", nil)
	logError("main", err)

}
 ```
As you can see the response function now looks at the thing parameter and writes out the contents of a file named the same 
as thing in the current directory.  Keep in mind this isn't production ready code, just a stub to code against.

For the above code to work you must run `go build` in the directory of your code and run the resulting executable.
Now you've done it, you have a working API that takes a parameter in and spits out json.

