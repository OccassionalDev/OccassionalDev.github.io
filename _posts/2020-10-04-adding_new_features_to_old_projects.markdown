---
layout: post
title:      "Adding New Features To Old Projects"
date:       2020-10-04 19:30:02 +0000
permalink:  adding_new_features_to_old_projects
---

Outside of the projects I've been working on, I feel that I've learned enough to add in the features I've wanted to add to them that I couldn't have before now. While I was working on my projects back a couple of months now, I had a list of features that I wanted to add in, but at the time I only added in a few features that I knew how to make for sure. However, I did my research, and practice, I've looked further and planned out how I'll add some of the features that I've wanted to add into my projects, I think it's time I finally add them in.

The first project that I'm going to improve is my old meetups/events app built using Rails, as I've looked into a few different features I could add to make the project better. A few of these features are as such:

* Geolocation
* Country and State Selection in Event Input
* Expanded Search form 
* User Profile Pictures
* Rich Text Formatting For Event Descriptions

Over the last week, I've looked into a few different gems, APIs, and other things I could implement to add in some of these features in to project. For example, for the country and state selection, I've already added in the [country_state_select](https://github.com/arvindvyas/Country-State-Select) gem to aid with selecting a country and state in the input form I've made for creating events. On top of that, I've been looking into other gems such as [ransack](https://github.com/activerecord-hackery/ransack) and [sunspot](https://github.com/sunspot/sunspot) to see how I can add more to my search form. 

However, the geolocation feature is something I'm going to have to look further into it, as it has many options I need to look into before I consider adding it in. One of the things I need to look further in to is changing my database from [SQLite](https://sqlite.org/index.html) over to a [PostgreSQL](https://sqlite.org/index.html) database. On top of changing my database, I have many options to look in to such as [Google's Geocoding API](https://developers.google.com/maps/documentation/geocoding/start), [Bing Maps](https://docs.microsoft.com/en-us/bingmaps/rest-services/) and [MapQuest](https://developer.mapquest.com/documentation/http://) to integrate with into the [geocoder](https://github.com/alexreisner/geocoder) gem.  

For now, I'm leaving my focus on getting the other features like the profile pictures, rich text formatting, and country and state selection in first before I focus on geolocation's implementation into the project. After adding these into my project, I'll move on to my React project and see how I can add in the features I've wanted to add in for a while as well. Going forward, as I learn more, I'll be adding more to my old projects, as I go along.
