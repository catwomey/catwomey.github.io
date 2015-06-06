---
layout: post
title:  "Java anti-patterns: ways to create unmaintainable code"
date:   2015-05-24
tags: java design patterns
---

##Introduction


##Translations with business logic

At some point in any non trivial application you will need to transform objects from one domain to another.  Perhaps you're merging two api calls, or an api call with a data call, etc.  When doing this transformation, a common mistake made is the introduction of business logic at this level.  At first glance it seems like a harmless thing to do, but it can very quickly spiral into a very hard to test and complex class.

Let's pretend we are creating an animal tracking program  

Consider this worst case scenario:



###Solution
Depending on the situation you can have a few different solutions to this problem.  If you can it would be preferable to retrieve the data you need to transform in a format that is easier to process.  If the data is from a database, create a better query.  If the data is from an api outside of your control more serious refactoring might be needed.

##Inline classes
###Problem
Inline classes bring more harm than good, expecially if you start embedding business logic in them (sadly I have seen this).  The worst offenders i've seen are inline predicates.  At first you think that there's not much harm, but in my opinion predicates are best used when you can easily test them and if you have them right in the middle of your code, suddenly you have to write integration tests instead of unit tests. 



###Solution

Take the above code and extrat the inline class to it's own file.  Suddenly you don't have to test the x method you just have to test the predicate.  Future you will be estatic that you had the fore thought to design things properly and potentially you will get to reuse the predicate. 


##Manipulating parameters and returning a value

Manipulating input and returning an output often leaves a smelly mess for a few reasons.  First you're violating the [single responsibility principle] by giving your method two or more tasks.  If you need to do more than one thing make more than one method!  Second, by manipulating input you're losing the original request and changing how other methods will interact with that input.



###Solution
I'm a big fan of designing by [contract], or at the very least adhereing to some of it's principles.  Basically you can think of a java method as a loose contract.  You expect x and y and will return z, if you ensure you don't change x and y and you return a new instance of z your application is less likely to contain bugs. This is something that I will pick up in code reviews frequently, and quite often it is trivial to write a test that will cause the original code (the code that changes input and supplies output) to fail in some condition.

[translator]:http://www.iro.umontreal.ca/~keller/Layla/translator.pdf
[contract]:http://en.wikipedia.org/wiki/Design_by_contract
[single responsibility principle]:http://en.wikipedia.org/wiki/Single_responsibility_principle