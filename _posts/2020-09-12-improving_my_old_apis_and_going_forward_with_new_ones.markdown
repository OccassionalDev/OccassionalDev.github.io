---
layout: post
title:      "Improving My Old APIs and Going Forward with New Ones"
date:       2020-09-13 01:45:40 +0000
permalink:  improving_my_old_apis_and_going_forward_with_new_ones
---

As I continue to learn Angular and continue to practice with, and eventually branch off to build a small project with it, I decided to build a new Rails API to go with my eventual project. Anticipating the moment I'll begin to work with Angular front-end, I first looked back on my old Rails APIs to reference what I did originally and to see how I made them. After having a look at my old project APIs, I came to realize all there was a lot I could add and refactor to them with. 

Over the week, I've looked further into Rails and found a few things I could add to my old APIs, and to all of my new ones as I go along with coding. The improvements I looked in to most to do with my APIs is versioning, securing them with sessions better, and additionally adding in tests. The big things I looked into most were with versioning and tests, as my projects before now have never included any type of testing and versioning added in them, so learning how to work with these was crucial for me to go on ahead with any API I create later on.

The first thing I've looked in to was versioning, as I wasn't too aware of how to version my APIs before and have never tried it as well. Looking around for resources on how to version an API, I came upon this [episode](http://railscasts.com/episodes/350-rest-api-versioning) of [RailsCast](http://railscasts.com/) that had covered exactly that to get me covered on the topic. After looking into the episode, I've decided to look further into what could make versioning easier, and that lead me to the [versioncake](https://github.com/bwillis/versioncake) gem, which helps with versioning Rails APIs. I looked into other options as well, such as [versionist](https://github.com/bploetz/versionist), but after working with [jbuilder](https://github.com/rails/jbuilder) on a previous project, I've taken a preference to [versioncake](https://github.com/bwillis/versioncake) over it for that, alongside the many configurations that come with it and its ability to work with [jbuilder](https://github.com/rails/jbuilder) easily as well.

After looking at versioning, I've moved on to looking at the types of testing I can do to add to my APIs and Rails applications in general, and I've decided to learn [Rspec](https://rspec.info/). I've started to look at Rspec from its documentation and a few videos on it to make sure I was getting everything down, and after that, I decided to give it a shot in the next API create. Looking back at my old APIs I may learn the Minitests that Rails comes with by default, as my old APIs already have the files in place for them. However, RSpec may be easier for me to drop into them as setting up Rspec is pretty easy, and I can speed it up with spring commands in case it starts to slow down. The spring commands for Rspec are pretty handy as well as I've learned as Rspec can tend to slow down, and that led me to this [guide](https://blog.kiprosh.com/use-spring-to-run-rspec-quicker/) on learning how to add it in.

Once I finished reading and playing around with Rspec, I then looked into how to secure my APIs better using Rails sessions and keeping the API tag attached to Rails as well. Previously, when I worked with my React project, my API had Rails sessions for the authentication, but I generated it as a regular Rails application but using it as an API instead. When I was messing around with Rspec on a test API, I was able to keep the regular API tag and have received no errors from setting a session from refollowing this [guide](https://pragmaticstudio.com/tutorials/rails-session-cookies-for-api-authentication), however, I've got an error coming in from RSpec on there being an invalid authenticity token. Looking further into this issue, it led me to this [Stack Overflow](https://stackoverflow.com/questions/42795288/rails-5-api-protect-from-forgery?answertab=votes#tab-top) question here. Looking through the top answer, after I changed the following line, everything in RSpec worked correctly and RSpec didn't give an issue:

```
  # Old Line
  protect_from_forgery with: :exception

  # Updated Line
  protect_from_forgery with: :exception, unless: -> { request.format.json? }
```

Now with versioncake, Rspec, and Rails sessions added to my tool belt, I can confidently go back and improve my old projects, and safely move on to making better APIs for future projects. With all that out of the way, I can move on to making my next new project for when I'm ready to work with Angular, by creating an API with what I've learned over this week.


 




