---
layout: post
title:      "React and Redux Project - Storyline"
date:       2020-07-30 21:14:57 +0000
permalink:  react_and_redux_project_-_storyline
---


Finishing my React Redux project was quite a challenge to go through this time around, I went through many issues during the process of creating this project, but I've learned a lot as I went on and learned from those issues. For my React and Redux project, I created a app called Storyline, which is a app to allow users to plan, create, and share stories and the characters that go along with them. With this project, I used a Rails backend, and used React and Redux for the frontend alongisde some other libaries to aid me in making some features or to make my developement smoother.

Stoyline as of now, only allows for users to create stories and characters and only have public stories, but as I go on, I'll add more features such as locations, worlds, character relations, and the reuse of characters to different stories and more.  However, the overall idea for Storyline is to allow a user to plan out a story and the basics of for a character and allow for other users to comment on those ideas. With this in mind lets move on to the things that I've used to make Storyline in its current state.

# Rails Backend
When I first started the project, the first thing I've focused on was my backend and how I'd work with it, this is where a real challenege came in for me, as I wanted to incorporate the usage of Rail's sessions and cookies to help with authentication this time around. However, this was a challenge on its own, as I was following this [guide](https://pragmaticstudio.com/tutorials/rails-session-cookies-for-api-authentication) on how to setup my Rails API for using sessions, everything did work but a weird issue did come up as I worked.

Initally, following that guide was fine, I was able to setup a session by doing `session[:user_id] = user.id` and no errors would come up, but an issue later on came up when I wanted to return something in my JSON responses that would use the session for particular checks. To give a good example of the problem, I had a boolean called `canEdit` that I wanted to pass in my JSON to tell my frontend that a user can edit a page or not, this is where just having my usual code as seen below wouldn't return true when it should of even though I had my current_user set:

```
def current_user=(user)
    session[:user_id] = user.id
end

def current_user
   User.find_by_id(session[:user_id])
end

def logged_in?
    current_user != nil
end 

def is_creator?(element)
    current_user.id === element.user_id 
end

def can_edit_or_destroy?(element)
   logged_in? && is_creator?(element)
end
```

To fix this particular issue, where my json wouldn't return, I kept my `current_user=` function under different name such as `set_user` and removed current_user, as it wasn't working as intended, and then I created a concern for my controllers, as seen here, and then changed all instances of `current_user` in my helpers to `@current_user`:

```
module CurrentUserConcern
    extend ActiveSupport::Concern 

    included do 
        before_action :set_current_user
    end 

    def set_current_user
        if session[:user_id]
            @current_user = User.find_by_id(session[:user_id])
        end 
    end 
end
```

What this concern will do, when included in to any controller will give the controller access to the `set_current_user` before action to set a instance variable called `current_user` when the session exists. Therefore, before any action in a controller that this concern is included in will set a `current_user` before any action takes place, this will guarentee that my functions that used `current_user` will not lose it and keep it for any of my helpers that I use in my controllers. Though, I did place this concern in my `ApplicationController`, but leaving it as a concern will allow me some flexibility with placing it where I need to move it to only a select few controllers if needed as I add more to my project. However, as a extra safety net to make sure my session was being set, I recreated my API without the `--api` flag and added `rack-cors` and a `sessions_store` to make sure my sessions were working properly and that my backend could be an API.

With this out of the way, lets brush over my database, as this makes up the heart of how my frontend will grab data, much like my other projects. Much like my last project, I've used [dbdiagram.io](https://dbdiagram.io/home) once again to plan my database, and used the [rails-erd gem](https://github.com/voormedia/rails-erd) to produce a visual of my database's relationships when done. Speaking of my database's relationships, they were relatively simple, aside from one:

* **Users** can have many Stories, Characters, and Comments
* **Stories** can have many Characters and Comments as Commentable, and it belongs to a user
* **Characters** can have many Comments as Commentable, and it belongs to a User and a Story
* **Comments** belong to Users, and belongs to Commentable, and is polymorphic

Everything here is straightforward besides the commentable part that is on stories, characters, and comments, I made a polymorphic relationship for my comments model instead of making a comments model seperately for each element of a story. To explain this better, I've made a polymorphic comments model to make it as a universal comments table for all the elements to a story, so each character, and story have their own comments that get stored in my comments model. I've went with this mainly due to the fact I wanted each element of a story that a user creates, another user will be able to comment on it, wiki page with comments that you see on a site like [fandom](https://www.fandom.com/), I've followed [guide](https://semaphoreci.com/blog/2017/08/16/polymorphic-associations-in-rails.html) to understand and learn how polymorphic models work. Additionally much like that guide, I created a concern for including  `has_many :comments :as => commentable` incase I want to include any scopes to organize comments as I add more elements to a story. 

On top of all of this, I created one more concern, one called `RecentlyCreated` to include all scopes I create for handling finding elements that are created recently, I may rename this concern later on as I contine to add more to the project, as I want it to find elements between a certain date, wether it be recently created, oldest created, or in between. For now, the `RecentlyCreated` concern only contains one scope as one can see:

```
module RecentlyCreated
    extend ActiveSupport::Concern
  
    included do
        scope :recently_created, -> {
            recent_date = Date.today - 1
            where("created_at >= ?", recent_date)
        }
    end
  end
```

Other than this, my controllers contain the usual CRUD actions and some nested routes, and unlike my javascript project, I've used the [Jbuilder gem](https://github.com/rails/jbuilder) this time around to make my JSON responses be created easily. With all that said and done, I can begin to talk about my frontend, where I've used React and Redux, and a few other dependenices to help me along the way.

# React and Redux Frontend
For the frontend, the first thing I've dealt with was creating the app itself, by using the create-a-react-app command and then adding in everything I'd be using afterwords. For dependincies besides react router, redux-thunk, and everything included with react and redux already, I've used [Redux-Persist](https://github.com/rt2zz/redux-persist) to help me persist data after a page refresh and save one less request, [Jshint](https://github.com/jshint/jshint/) for javascript debugging, and [Axios](https://github.com/axios/axios) for handling my requests to my API.

For my frontend, everything followed a pattern for the file structure, in which I had my components seperated and organized by what they're used for, containers, redux specific files, and helpers I've used throughout the other files. Mostly everything I've done with my reducers is I've seperated them out depending on wether it was a list or a viewed page and then refactored my actions in to their own categories for what they're for. After that, I went along building my pages one at a time in function, and only persisted my currentUser reducer in Redux-Perist to save a request on page load, and to also keep a user logged in on a page refresh if they've logged in.

Other than this, my containers followed the pattern of on load, fetch the data, pass them as props, and render the data needed on load, and imported all needed components for a container. As i built up a container for a page that would get loaded in to my App.js file, which handled my routes, I debugged and tested everything as I went along. Though most of my major roadblock with this portion of the project, as stated earlier, came from my API not returning booleans that were based on the logged in user not working correctly, however, once that was fixed, the rest of my pages that used such data were set to be finished. The more I worked on this portion building my components, and containers, I've learned a lot about the React-Router, and how to order my routes and make sure by using things like `Switch`, `exact`, and `strict` on my routes to make sure only that one component for that route loads when a specific url is used.

With this, I've let [axios](https://github.com/axios/axios) handle format my requests and JSON data, while I only import the request url and data that needed to be passed through to my API. Using axios, I didn't need to constantly retype my base url or the configuration header with credentials, as I configured it with global defaults in my index.js file like so:

```
axios.defaults.baseURL = "http://localhost:3001";
axios.defaults.withCredentials = true;
```

With all that done and making sure all the correct data gets rendered and all my requests work properly, I've finished my project for what I've wanted to add for now. However, as time goes on, I'll add in more features and elements to it as stated earlier, such as marking a story as private or public, locations, moving characters between stories or reusing them, and more. 

# Final Thoughts
Now that everything is done, I've learned quite a lot while working on every aspect of my project, like learning how to make concern through ActiveSupport, learning about polymorphic models, how to add in and configure things like Redux-Persist, and looking libaries to help me along my way to add more to my project. With each step of the way as I built new features and pages, each step has brought me in to understanding React and Redux more while learning more about Rails as well. This project has been quite a stepping stone for me as I ran in to errors and learned how to fix and debug them along the way, I'm happy with the things I've learned and I am happy to move on to what I can learn next.










