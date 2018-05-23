---
layout: post
title:      "Corroboration App with Sinatra"
date:       2018-05-23 18:36:43 +0000
permalink:  corroboration_app_with_sinatra
---

After what felt like endless tinkering, I'm finally nearing the end of my Sinatra project!!! Surprisingly, the most challenging part was figuring out how I wanted to structure my website, and how I wanted it to look and feel to the user (not using Sinatra and setting up a working web app, which actually seemed pretty simple). 

The basic idea was I wanted to create a Corroboration app; a place for victims of abuse to post information about their abusers and find corroborators. As many more victims are feeling encouraged to speak out about their experiences of abuse using social media and other outlets, I feel there should be a way to organize this kind of information to be easily searchable. Finding corroboration is often an integral step in pursuing legal action, and maybe an app like this could serve to connect people who have endured similar experiences. There are also many state resources for finding public records about past criminal convictions, as well as the sex offender registry. Maybe in the future, these records can be integrated into this app so they can serve to verify information that is posted by users. I've included some concerns and ideas about expanding this app in the README, and I am open to different ideas. 

First things first, I set up my file tree- adding model, views, controllers, Gemfile, Rakefile, environment. This step was largely inspired by the many, many other labs I've completed using Sinatra to make a variety of different web apps. 

#### Example Gemfile
```
source 'http://rubygems.org'

gem 'sinatra'
gem 'activerecord', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
gem 'rake'
gem 'require_all'
gem 'sqlite3'
gem 'thin'
gem 'shotgun'
gem 'pry'
gem 'bcrypt'
gem "tux"
gem 'rack-flash3'

group :test do
  gem 'rspec'
  gem 'capybara'
  gem 'rack-test'
  gem 'database_cleaner', git: 'https://github.com/bmabey/database_cleaner.git'
end
```

#### Example Rakefile
```
ENV["SINATRA_ENV"] ||= "development"

require_relative './config/environment'
require 'sinatra/activerecord/rake'
```

#### Example config/environment.rb
```
ENV['SINATRA_ENV'] ||= "development"

require 'bundler/setup'
Bundler.require(:default, ENV['SINATRA_ENV'])

ActiveRecord::Base.establish_connection(
  :adapter => "sqlite3",
  :database => "db/#{ENV['SINATRA_ENV']}.sqlite"
)

require_all 'app'
```


Once all of my gems and rake tasks were set up, I was able to create some tables in my database using ActiveRecord migrations. 

#### Example User Migration
`rake db:create_migration NAME=create_users`

```
class CreateUsers < ActiveRecord::Migration[5.2]
  def change
    create_table :users do |t|
      t.string :email
      t.string :username
      t.string :password_digest
    end
  end
end
```

`rake db:migrate`

I decided on three different tables in my database that correlate to three different models: Users, Claims, and Subjects. Users have an email, username, and password. I used the bcript gem to be sure that passwords would be encripted before they are stored in the database. Claims are timestamped, so an instance of the Claim class stores information about the date and time it was posted, and the date an time it was updated. They also have content, a location, a contact (when making a new claim or editing a claim, a user can choose if they want their contact information to be associated with the claim for future corroborators to find). Claims also have a user_id and a subject_id because a claim belongs to the User that posted it, as well as the Subect it describes. Subjects have a name.

Then I created my models. Each of my models inherits from ActiveRecord::Base, and I included the following macros:

For User Class:
`  has_secure_password` and `has_many :claims`

For Claim Class:
`  belongs_to :user` and `belongs_to :subject`

For Subject Class:
`  has_many :claims`

I also created a concerns folder in my models folder, where I created two modules: Slugifiable and Findable, which I included and extended in my Subject class respectively. The Slugifiable module, when included in the Subject class, allows us to call .slug on an instance to replace spaces with `-` and make all letters lowercase. This is useful when making URL routes for different subject names. The Findable module, when extended in the Subject class, allows us to call .find_by_slug(slug) on the Subject class itself to return an instance whose slug matches the slug given as an argument.

Next, I created the Application Controller, which inherits from Sinatra::Base and sets up our basic actions and routes. I enable sessions here, which allow user information to be stored to keep a user logged in through the different pages of my site. I set my encription key for my passwords to "corroboration_is_the_key." I also created two helper methods, `logged_in?` and `current_user` to help me easily check the session to see if a user is already logged in or not. I made controller actions, `get '/'` and `get '/terms'` which render the index.erb and terms.erb views.

#### Example application_controller.rb
```
require './config/environment'

class ApplicationController < Sinatra::Base

  configure do
    set :public_folder, 'public'
    set :views, 'app/views'
    enable :sessions
    set :session_secret, "corroboration_is_the_key"
  end

  get '/' do
    erb :"/index"
  end

  get '/terms' do
    erb :"/terms"
  end

  helpers do
    def logged_in?
      !!session[:username]
    end

    def current_user
      User.find_by(username: session[:username])

    end
  end
end
```

I also used a layout.erb file and the `yield` function to encapsulate certain features that I wanted to be shown on every page, such as the title, and general styling.

Next, I created my other controllers for each of my models, allowing each of them to inherit from ApplicationController. I made different controllers for User, Claim, and Subject, and corresponding views to be rendered.

In the UserController, I used rack-flash to show flash messages if a user enters the wrong username or password or leaves a field blank when logging in, or if a user tries to sign up with a username that is already taken. In the ClaimController, I also used flash messages to make sure the user fills out all fields when entering a new claim, and editing a claim.

The UserController handles user signup, login, logout, and finding corroborators. When a user logs in, their login information is authenticated (method given to us by the bcript gem and the `has_secure_password` macro). Once authenticated we set the session: `session[:username] = @user.username`, then they are brought to the user/show.erb page, which has links for different things you can do on the website: make a new claim, view all past claims, search subjects by name, find corroborators, and logout. Logging out just clears the session and brings the user back to the index page. The find_corroborators route checks if the user is logged in, sets @user equal to the current_user (using our helper methods), and then renders our view:

```
<h2>Find Corroborators</h2>
  <% @user.claims.each do |users_claim| %>
    <p>Corroborator contact for <a href="/subjects/<%=users_claim.subject.slug%>"><%= users_claim.subject.name %></a></p>
    <% users_claim.subject.claims.each do |subjects_claim| %>
       <% if subjects_claim.user != @user %>
         <ul>
           <li><%= subjects_claim.contact %></li>
         </ul>
        <% end %>
    <%  end %>
  <% end %>
```

So here we are iterating over each claim made by the user, and posting the contact information associated with other claims about the same subjects.

The ClaimsController handles viewing all claims made by `current_user`, viewing a single claim made by `current_user`, making a new claim, editing a claim, and deleting a claim. At every route, we user our helper methods to make sure the user is logged in, and the claim belongs to the current user. A current user can only view claims made by other users on the subject show page, but they cannot view their individual show pages, or see the options to edit or delete posts that do not belong to them. On the new claim and edit claim form, the params hash is structured like this:

```
{"subject"=>{"name"=>"name"},
 "claim"=>{"location"=>"location", "content"=>"content"},
 "user"=>{"contact"=>"no"},
 "agree"=>"yes"}
```

The SubjectController handles searching for a subject by name, the subject show page, and showing a view if no subject is found in the search. Again, I use the helper methods to make sure a user is logged in. I also used the `titleize` method to normalize subject names and make sure we don't run into issues searching for and saving Subject names based on capitalization.

Thank you for reading. Here's the link to my walkthrough video and githup repository.

[Walkthrough Video](https://www.youtube.com/watch?v=8pMHOwvo7A0&t=3s)

[Github Repository](https://github.com/MFeuer23/Corroborate)





