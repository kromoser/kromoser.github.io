---
layout: post
title:  "Why does my `nil` feel so `.empty?`"
date:   2016-07-29 18:38:41 +0000
---


Recently, in a Sinatra lesson, I was writing an application controller that involved a user signing up on a website. As such, the controller needed to perform a very basic function, making sure that the username and password fields were not blank when the sign-up form was submitted. If the fields were filled out, it brings the user to the log-in page, and if one or both were empty, it would display an error page.

I thought, *okay, this is easy. I'll just check to see if my `params` values are `nil`.* But it didn't work, and I was really puzzled. It seemed obvious to me, that if a field were left blank, the value for the field in my `params` hash would be `nil`. Why wasn't it working?

It's really quite basic, as it turned out, a foundational truth about Ruby that I had forgotten. But first, here's a more clear illustration of the problem:

```
s = String.new("Hello, world")
=> "Hello, world"
s.nil?
=> false #obviously!
s = String.new("")
=> ""
s.nil?
=> false #Huh? But there's nothing there?
```

Well, here's how I've come to understand it. The `.nil?` method is a method that will only return `true` if it is called on the object `nil`, which belongs to `NilClass`. Let's take a look:

```
n = nil
=> nil
n.nil?
=> true
n.class
=> NilClass
```

Only the object `nil` itself is `nil`. `nil` is not nothing. It's `nil`. How zen.

But now, it becomes clear if we ask what class "" is:

```
s = ""
=> ""
s.class
=> String
```

An empty string, which belongs to the `String` class, isn't `nil`. It can't be because it literally isn't `nil`. But, it is empty. And of course, because Ruby loves us, there's a method for that:

```
s = String.new("")
=> ""
s.nil?
=> false
s.empty?
=> true
```

The whole time I was trying to say `.empty?` but kept saying `.nil?`. So, maybe it's obvious. I guess it should be obvious. I know I learned this at some point. But sometimes, it's important for me to re-teach myself some of the basic truths about Ruby. Often, it leads to a greater intuitive unerstanding of its core workings.


