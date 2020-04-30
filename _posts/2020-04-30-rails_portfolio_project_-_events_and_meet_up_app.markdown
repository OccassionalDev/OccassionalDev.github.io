---
layout: post
title:      "Rails Portfolio Project - Events and Meet Up App"
date:       2020-04-30 17:41:36 +0000
permalink:  rails_portfolio_project_-_events_and_meet_up_app
---


During the last couple two weeks, I worked on my rails portfolio project, which is a application that allows users to create events, reserve to events, and review events they reserved to. Throughout the whole process of making this application, I've grown to be more comfortable with rails and making and designing databases for my applications using activerecord. Overall, my idea for the design of the project came to be pretty similar to my sinatra project, in which I planned out what I needed for my database first and then designed the links and everything to go with it after.

# Signup and Login Pages
Much like my sinatra project, I've used [dbdiagram.io](https://dbdiagram.io/home) to once again plan my database and the type of data I needed to make my project around, this being a few tables for users, events, reviews, reservations all assocating with each other in different ways. Ultimately, I ended up with the following for the database:

* Users can have many events, reservations, and reviews, while also having reserved events through the events table
* Events can have many users, reservations, and reviews, and have many attending users through the users table
* Reservations and reviews belong to users and events

With these associations set up in the tables respected models, I was off to start creating a signup and login page. For the login page I used similar code from my sinatra project in my sessions which was this:

```
            user = User.find_by_email(params[:email])
            
            if user && user.authenticate(params[:password])
                successful_login(user)
            else 
                redirect_to login_path, alert: "Email or password is invalid"
            end 
```

Much like my sinatra project, I still take in a user, try to find their account via their email, and if their password is an authenticated password, the user can be logged in succesfully. Though, this time around I have a  successful_login function, which sets the session with a user id and redirects the user to the homepage with an alert saying "Successfully logged in as <user's name here>" and after that, the user is fully logged in, and this is just one part that goes for the regular login, not the github one. However, my signup page remains mostly the same but this time with a password confirmation section, where a user has to enter their password one more time to confirm it, and if all parts of the form is valid, the information is then saved, the user is logged in, and given an alert saying that their account is created. 
# Events, Reservations, and Reviews

After creating the signup and login pages, I moved on to allowing a user to create an event and have it be listed on the homepage, this was made in a standard way of having a form input then saving it to the database if there's nothing wrong with the information placed in. If there was anything wrong, and much like my signup page, if there's anything wrong the page will re-render and show off the errors that didn't allow the event to be saved much like the account being unable to be created.

Once events could be created, I moved on to allowing users to create reservations and reserve themselves in to events, this function was a little tricky as I was having a hard time making a create reservation method in the controller to allow for a reservation to be made. My issue with this was that it could only work with the show page of the event you're currently on, so what I did instead of making a create event in the reservations controller, I created a reserve path in the events controller to have a event be reserved, then in the reservations controller, a destroy path to cancel a reservation. Another thing I've done was that I made a few helper methods to detect if a user can reserve to an event if they follow two conditions, which is that a user has to be logged in, and the user cannot be the event's creator to make an event, if those two conditions are met, a user can reserve to an event. Though, later on, I did add in the ability for a user to edit and update events.

When events were able to be reserved, and the reservations could be canceled succesfully, I moved on to the next function, reviews. Reviews were less tricky to create, as they were a nested resource that could be created, but not edited, and couldn't be destroyed after creation. With my reviews controller, everything was built around it being purely nested, so my code for reviews constantly had to pick up the id of the event that the review was for when the review was being listed as a index or being created. Another thing I did was place a if statement around in new in the reviews controller to see if a user already left a review, which was this code here:

```
    def new
        if does_review_exist?(params[:event_id])
            redirect_to event_path(Event.find(params[:event_id])), alert: "You've already submitted a review for this event."
        else 
            @review = Review.new(event_id: params[:event_id])
        end 
    end
```

What this code does is look up the event in the user's reviews, and if theres no review to be found, a user can be brought to the new page without a problem to submit a review, if a review is found, the user is redirected to the show page with an alert saying they've already created a review for the event. Once this was made, I moved on to getting to the last parts of my project, which was setting up omniauth, the search bar, and making the scope method.

# The Search Bar, Scope Method, and Omniauth
Out of all three of these features, the hardest one to have added in was omniauth, as I was having a issue with getting the link to work that would trigger the callback for the event would give me a csrf error. To fix this,   I had to use the gem by the name of 'omniauth-rails_csrf_protection' and add a method: "POST" on to my github link and change the route for the callback one last time and change around the functions that created and found the account, after that things were set. The other two, the search bar and the scope method were pretty easy to add in to program.

For the scope method, I made my scope method show recently created reviews, and this method queryed out all reviews where their created_at column shows that the date the review was created at was made at least 2 days ago or now. The code for the scope method is as follow:

```
    scope :recent_reviews, -> {
        recent_date = DateTime.now - 2
        where("created_at >= ?", recent_date)
    }
```

To keep the date updated all the time, I made a recent_date variable to store the DateTime of 2 days ago and compared it to the created time of the review, if a review follows that, it gets placed in to the list of recent reviews and then can be listed on the recent reviews page. The search bar is similar to this for a query, but it uses a "LIKE" query instead to find similar looking titles for events:

```
    def self.search(query)
        if !query.empty?
            self.where("TITLE LIKE ?", "%#{query}%")
        else 
            self.all
        end 
    end
```

Unlike the scope method, this will return all the events created if the query is left empty in the search bar. Addittionally, if there are no results found, the page that returns the search results, will say that there are no results found for the query.

Lastly, comes the omniauth login, which took me quite some time to fix due to the csrf error, however, once fixed, making a account login was easier. For a account to be used to login I used two seperate methods to help find or create an account for a omniauth login for github, and these are:

```
    def self.create_oauth_user(hash)
        pw = SecureRandom.hex(16)
        
        create! do |u|
            u.uid = hash[:uid]
            u.name = hash[:info][:nickname]
            u.email = hash[:info][:email]
            u.password = pw 
            u.password_confirmation = pw
        end 
    end 

    def self.find_or_create_from_auth_hash(hash)
        user = find_by(uid: hash[:uid])

        if !user 
            create_oauth_user(hash)
            user = find_by(uid: hash[:uid])
        end 

        user
    end 
```

These two methods work together to find or create an account for a github user to login, it starts by finding the user by their uid, and if it doesn't exist, the function will create a account for the user and then locate it and return it so the login becomes possible. After that, through the controller, the succesful_login function is called and then the session becomes set and then the user is logged in and ready to create, reserve, review, edit and update their own events.

# Final Thoughts
This project was a fun one despite the issue I had for some time setting up omniauth, but I got very comfortable with rails and setting up databases with this project. With moving on to JavaScript, I'll have an easy time setting up rails for the next project and feeling at home with JavaScript, as I have some experince with writing code in Javascript. This project gave me a lot of experince with rails and I enjoyed making it well.

With going on to JavaScript, I'll bring on my new experinces of writing custom validators and querying methods in models using ActiveRecord and feel right at home writing the code with JavaScript. With the JavaScript project, I may try something I never tried with it before to give myself a bit of a challenge it.




