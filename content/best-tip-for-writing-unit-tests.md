---
title: "The Single Best Tip for Writing Unit Tests"
subtitle: The Anti-Zoolander Center for Developers Who Can't Test Good
date: 2019-08-14T03:03:18.687Z
draft: false
type: post
tags: ["PHP", "Unit Testing",  "tips and tricks"]
description:
image: 
cover: https://i.pinimg.com/originals/21/77/4c/21774c49405bbaf6b2255da1a74bdfa7.gif
summary: A unit testing tip that will save you tons of time and effort inspired by Zoolander.
---

Derek Zoolander always wants things *so big*!
![](http://www.quickmeme.com/img/89/89dd7fe7cae4737cb3af42080838b498d1d6dca75c66027ae4564d68aaa5f6fe.jpg)

Are you ready to find out the _single best_ method to help writing
unit tests? Here it is:

*SMALL CLASS SIZES!*

Whenever possible, split your classes. They should only be doing one thing.
If the number of methods is small, then the amount of testing
going into it will be a lot easier to manage. 

Now a unit test can test just a couple methods. You're not running tests
on something that does too much.

In _Clean Code_, Uncle Bob Martin writes...

> The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that. Functions should not be 100 lines long. Functions should hardly ever be 20 lines long.

The same could be said of classes. Break it into multiple classes. Use
the smaller ones as constructor dependencies in the larger. This will
help your testing immensely. It will also prevent you from testing
an enormous amount of private methods (because you're doing too much)!

Don't be like Zoolander. You must want it small!
![](/uploads/zoolander-unit-testing.gif)