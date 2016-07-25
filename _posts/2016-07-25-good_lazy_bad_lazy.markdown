---
layout: post
title:  "Good lazy, bad lazy"
date:   2016-07-25 04:23:00 +0000
---


I'm the manager of a retail shop. Part of my job is maintaining our online presence, and right now, I'm working on a redesign of our website. It's a static site, so it's pretty basic HTML/CSS and a bit of simple jQuery. At the same time in Learn, I'm becoming familiar with Sinatra and ERB. During the day I find myself copying and pasting code snippets andlessly as I tweak a menu or change a `toggleClass` handler. And in the evening, I'm thinking about how to let ERB populate a webpage by writing a few methods and setting a few parameters. All of this has me thinking about laziness.

As we've all hear a bunch of times, programmers are lazy. I'm certainly lazy, for one. But I'm finding there's good lazy and bad lazy. Recently, I was writing a bit of code that hides and shows a submenu depending on what a user clicks and what page she's currently viewing. I knew I could mindlessly do it by writing functions that explicitly referred to the different elements I needed to control. I thought to myself *I could abstract this, so everything would just work, but then I'd have to spend time thinking about how to do that, and I could just bang out some extra lines and it'll work for now.* I was being bad lazy. Of course, two days later, when I had to make a tiny change to the menu, I had to rewrite the whole function, and then make that change on every page.

I should have been good lazy. That is, I should have been thoughtful in the present so I could be lazy in the future. Put in the thought now, so future changes would be easy. After all, wouldn't I rather spend my time now brainstorming a clever solution than spend it later mindlessly changing explicit details in my code. That way I could have finished the changes, taken a long lunch break, and enjoyed the fruits of my *good* laziness.
