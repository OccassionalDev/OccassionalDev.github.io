---
layout: post
title:      "JavaScript Portfolio Project - Investments Planner"
date:       2020-06-05 00:54:09 +0000
permalink:  javascript_portfolio_project_-_investments_planner
---


Over the last week I worked on my single page javascript application which ultilizes PostgreSQL, a Rails backend api, JavaScript for the front end, and the Chart.js library to create a planner for a investment portfolio. The idea of this project is that a user has a table and allows a user to create, and delete investments that they add to their planner and the chart on the page adapts to it by showing how much a user invests in to a particular industry. My process for going along with this project was realtively the same as my Rails project, but with the added difference of working with JavaScript and making fetch requests to the backend to work.
# Starting the Backend
Instead of rushing in to the frontend of my project, I started with the backend to get the database, and needed models and controllers setup in the project first. This time around, just like my Rails project, I started with the database creation as always, but without the use of [dbdiagram.io](https://dbdiagram.io/home), as my project only uses a single has many relationship included in it. For this database, I only had users and investments, and users can have many investments, which was the only needed relationship I needed for the project to go on with, and with that relationship setup, I continued on to make my controllers and models.

After the database was setup, my models were the next thing on my list to create, which was done by using my usual validations using the `bcrypt` gem for user passwords and had setup validations for the presence of name, and email, and also gave emails a uniqueness validation for no repeating emails. Meanwhile, the investments model only had validations for the presence of the name, industry, type of investment, and the number of shares of a investment. With the models done, I moved on to setting up basic controller actions to allow a user to create an account, log in, create an investment, and delete an investment.

Once the controller actions were made I moved on to the frontend, but throughout the process of making the frontend I made a few changes to the controllers and the investment model. To start with these changes, I first went to the investment model to create two different methods which would make my time using Chart.js much eaiser later on. These created methods are the following:

```
  def self.industries
    self.all.pluck(:industry).uniq
  end 

  def self.industry_shares
    industries = self.industries
    shares_list = []

    industries.each do |industry|
      shares_list.push(self.where(:industry => industry).sum(:shares))
    end 

    return shares_list
  end 
```


What these two methods do is that they return arrays that I would have my backend api return so I can easily create a chart with on my front end. To be more specific on what these methods are for in particular, `industries` collects a unique list of all the industries a user has, which would later be used as the list of labels. The other method, `industry_shares` gets the list of industries from `industries` and then for each industry collects a list of the total amount of shares a user has in each industry, this array would be used as the dataset. With these methods setup, I then used them for the changes I made in controllers so I would be able to use these arrays in my front end.

To get the data moved on in to my frontend, I made a new method in the application controller called `user_hash`, which would be used to return the arrays I used for Chart.js. The `user_hash` method was written as the following:

```
    def user_hash(user)
        {
            id: user.id,
            name: user.name,
            password_digest: user.password_digest,
            recovery_password_digest: user.recovery_password_digest,
            investments: user.investments,
            industries: user.investments.industries,
            industry_shares: user.investments.industry_shares
        }
    end 
```


With this method defined, I immediately used it all throughout my controllers for when the main page of my single page application would be needed to be rendered. With having the `industries` and `industry_shares` arrays readily available on the main page of my program, the created chart will always be ready to be shown on the page. With that out of the way lets move on to the frontend of my application.
# Creating the Frontend
On the frontend of my application, I made seperated classes that would be used as different instances throughout my main class that rendered everything regarding the page, which was my `App` class in my `app.js` file. One of the first things I did in my project was setup an adapter object that would be readily used throughout the program to use fetch requests easily, and to also cutdown on repeating code.

Like my adapter class, I made my other classes that were used in `App` be seperated and only contain functions where their functionality had gone along with the name of class. A good example of this is my `Users` class, which held the requests for logging in and signing up, and also the data made for storing the currentUser and the HTML for rendering the sign up and login forms. With these classes created, I made them all initialized in the `App`'s consturctor to get them insanced and able to be used all throughout `App` to create event listeners and render the needed data for the main page.

The only thing left uninitialized on the creation of `App` is the diagram variable, which awaits for a user to be logged in first before being created, as Chart.js requires a canvas tag to already exist before a chart is created. Once the canvas tag is rendered, I allow for the diagram class to be created, then have it render the chart that gets created on it being initialized. With diagram being created on the main page's render function in `App`, the functions inside the `Diagram` class can be ran without a problem while it utilizes the `industries`, and `industry_shares` arrays in the JSON responses upon adding or removing a investment to update the chart on the page. 
# Final Thoughts
After finishing this project, I didn't get in everything I wanted to add in, so I do aim to come back to this project and refactor it in a few ways, like adding in a edit function that utilizes the unused update action I have in my api. Another  way I would improve this project would be to change the uses of my classes to be changed in to modules and import everything instead of instancing it through a new class variable. Though, throughout working on this project, I learned how to read documentation for code libraries better through using Chart.js, and getting used to making fetch requests.

Overall, this project was a bit of a challenge for me when it came to getting used to setting everything up for a single page instead of a multi-page application, as I am more used to multi-page applications when it comes to using JavaScript. However, I will come back and modify this project more in different ways when it comes to the code, adding in new features, and making the project more secure when it comes to users logging in to their accounts.
