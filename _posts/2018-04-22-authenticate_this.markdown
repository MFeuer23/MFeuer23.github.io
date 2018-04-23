---
layout: post
title:      "Authenticate This"
date:       2018-04-23 00:56:30 +0000
permalink:  authenticate_this
---


It's been interesting starting to build applications with Models, Views, and Controllers. It feels like I'm starting to understand what goes on behind the scenes when you log into a website.

First comes the database. You can set up ActiveRecord and use migrations to create your table with rake tasks, making sure to set up your gemfile, environment, and rakefile:

### A sample Gemfile
```
source 'https://rubygems.org'

gem 'sinatra'
gem 'activerecord'
gem 'sinatra-activerecord'
gem 'rake'
gem 'bcrypt'
gem 'require_all'
gem 'capybara'
gem 'rack_session_access'
gem 'rack-test'
gem 'shotgun'

group :development do
  gem 'pry'
  gem 'sqlite3'
  gem 'rspec'
  gem 'tux'
end
```

### A sample config/environment
```
require 'bundler'
Bundler.require

configure :development do
	set :database, {adapter: "sqlite3", database: "db/database.sqlite3"}
end
require_relative '../app/controllers/application_controller.rb'
require_all 'app/models'
```

### A sample Rakefile
```
require "./config/environment"
require "sinatra/activerecord/rake"
```
 
 The 'bcript' gem is used to encrypt your passwords. When you use a migration to create your users table, you can label your password column as password_digest. Since we never want to store our users' passwords in plain text in our database, this runs the passwords through a hashing algorithm. The hashed passwords are then stored, protecting the site's users if a hacker gets ahold of the database.
 
 Then we create a model, User, that inherits from ActiveRecord::Base and has the macro, has_secure_password. The has_secure_password macro gives us some helpful methods such as .authenticate, which we can use to check if the user input on the form matches a username and password that is stored in the database. [More Info Here](https://apidock.com/rails/v4.0.2/ActiveModel/SecurePassword/ClassMethods/has_secure_password)
 
 Then, we want to create our controller. It is a class that inherits from Sinatra::Base. First, we have to require our environment and our model, then inside our class, we have a bit of configuration to enable our sessions. Sessions allow us to keep track of a user by creating a hash that is stored on the server and passed to the user through cookies. We can access this hash in all of our views, and even change it so it can store the current user ID while a user is logged in. 
 
```
require "./config/environment"
require "./app/models/user"
class ApplicationController < Sinatra::Base

  configure do
    set :views, "app/views"
    enable :sessions
    set :session_secret, "password_security"
  end
	
end
``` 
We can use helper methods within the controller to help our authentication process. Helper methods make our logic within the controller a little easier. We can use them to figure out if we should redirect the user, or render a page, based on whether or not the user is logged in, and which user it is.

```
helpers do
    def logged_in?
      !!session[:user_id]
    end

    def current_user
      User.find(session[:user_id])
    end
  end
```
We set up our routes and views. Our index page might greet the user and provide links to sign in or register. We can make a route in our controller that renders the index.erb file when a get request is sent to '/'

The '/login' and '/signup' routes each display similar forms with an input for username, password, and a submit button. The login form has an action attribute of '/login' and a method of 'post' - the registration form has an action attribute of '/signup' and a method of 'post'

Our controller receives the information the user submitted in the params hash. The post '/signup' action in the controller will try to create a new instance of user with the attributes provided by the user in params, and persist the data to the database. If the username or password is left blank, we can redirect to an error page with a link that directs back to the /signup page. Otherwise, we can sign them in right there, or redirect to our login page.

The post '/login' action in the controller will search the database for the params entered by the user. If the user is found, and the password is authenticated, then we add a new key of :user_id to our session hash, and set it equal to the id attribute of our user instance. Then, we are brought to the current user's account page. If not, we are brought to the error page.

```
 post "/login" do
   
    user = User.find_by(username: params[:username])
    if user && user.authenticate(params[:password])
      session[:user_id] = user.id
      redirect '/account'
    else
      redirect '/failure'
    end
   
  end
```

We can then use our helper methods to render the correct information on the '/account' page by making sure the user is logged in, and checking the current user. If the user is not logged in, we can redired to the error page. If the user is logged in, we can set the current user equal to an instance variable to be passed to the account.erb view so we can access and display any attribute we would like.

To logout, we simply clear the session hash. 

```
get "/logout" do
    session.clear
    redirect "/"
  end
```

 


