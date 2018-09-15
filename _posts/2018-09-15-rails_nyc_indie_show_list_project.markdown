---
layout: post
title:      "Rails NYC Indie Show List Project"
date:       2018-09-15 16:11:55 +0000
permalink:  rails_nyc_indie_show_list_project
---


I'm very excited to be closing the book on my Rails Project. I've met all of the requirements now, so at this point I'm really just tinkering with the look and feel of my web application and working on refactoring.  I wanted to use this project as an opportunity to practice using bootstrap, a logo image for the navbar, and user image uploading. I also used Devise for the first time to set up artist registrations and log in, as well as omniauth through facebook. I'm also considering using a geokit gem to show a user which venues or shows are nearest to them- that may be the last step to see if I can implement it. Could be fun.

At first, this project seemed a bit daunting. The first thing I did was chart out my objects and object relationships. I came up with Artist, Show, and Venue as my models. An Artist has many Shows and has many Venues through Shows. Shows belong to one Artist and one Venue. Venues have many Shows and have many Artists through Shows. This is what I went with as a simplified version, with Shows acting as a joins table with other submittible attributes. Originally I wanted Shows and Artists to have a many-to-many relationship because at most indie music shows, there is a collection of artists who play together on the same bill. However, I ultimately decided to simplify this for the scope of my project due to the following issues: what if one artist adds a show that involves another artist who does not have an account on my web app? what if two artists both add the same show with slightly different details? what would be the joins table?

To develop this project further in the future, I might try and implement a system where multiple artists can be listed on the same show (even if they don't all have accounts), and perhaps there would be a way to merge shows that are posted multiple times by participating artists.

My next step was running the `rails new` command and then implementing Bootstrap. There was a lesson on bootstrap toward the end of the HTML & CSS module, but I hadn't really tried using it apart from that. Overall, it was pretty easy, apart from one bug with the routing for delete requests. I was able to get around this if I used buttons to sign out and delete shows/venues instead of links. I think once I complete this project and go on to learn more about Javascript, I'll have a better understanding of how to fix this, but with links (even if I specified the method was a Delete method), it seems to default to a get request. Overall I had fun figuring it out, and I look forward to practicing more UX and styling in my future projects. 

To implement Devise, I basically followed along [this tutorial](https://github.com/plataformatec/devise), and it was relatively simple. I had to make a custom controller to add additional attributes to my sign-up form (I realized otherwise these attributes were not saving because of strong params):
```
class RegistrationsController < Devise::RegistrationsController
  private
   def sign_up_params
    params.require(:artist).permit(:name, :bio, :genre, :email, :password, :password_confirmation)
  end
end
```
and route to this controller in config/routes.rb:
```
devise_for :artists, controllers: { omniauth_callbacks: 'artists/omniauth_callbacks', registrations: 'registrations' }
```

It was also pretty easy to implement Omniauth with devise, following along the above linked tutorial. 

I used a nested resource for shows that belong to a certain artist, added simple model validations and displayed error messages in my views, and made an ActiveRecord scope method for shows tonight that links to the home page.

I used the devise helper methods and some logic so that certain features of the site are only available if a user has registered as an artist. For example, only an artist can add a new show, add a new venue, or edit shows and venues. An artist can't add or edit shows for a different artist.

I also figured out how to use an image for my navbar logo and allow artists to upload a profile image! I uploaded a logo image jpg file into my app/assets/images folder, and linked to it in my app/views/layouts/application.html.erb file:

```
<a class="navbar-brand" href="/assets/nycindieshowlist1.jpg"><%= image_tag "nycindieshowlist1.jpg" %></a>
```

I used CarrierWave and Mini Magick gems to handle artist profile photos. **remove thumb from app/uploaders/image_uploader**
So first I added 

`gem 'carrierwave'
gem 'mini_magick' # For different versions of an image`

to my gemfile and ran bundle install. Then, I added :image to my artist_params private method in my artists controller, so my image could make it through strong params. I mounted my uploader to my artist model by adding: 

`mount_uploader :image, ImageUploader`

Then I made an image uploader file- app/uploaders/image_uploader.rb
```
class ImageUploader < CarrierWave::Uploader::Base

  include CarrierWave::MiniMagick
    def extension_white_list
     %w(jpg jpeg gif png)
    end
		
  # Choose what kind of storage to use for this uploader:
  storage :file

  # This is a sensible default for uploaders that are meant to be mounted:
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end
 
   # Create different versions of uploaded files:
   version :profile do
     process resize_to_fit: [300, 200]
   end
 
end
```


Then I added an image field to my artist edit.html.erb view:
```
  <%= f.label :image %>
  <%= f.file_field :image %>
```

and showed the image on the show page:
```
<%= image_tag(@artist.image_url(:profile).to_s) %>
```

and also added a new column to my artists table, image (as a string) and voila! not as difficult as I thought. Not sure if Mini Magick was necessary, or if I could have just used scale. Using Mini Magick, I can also make a thumbnail version of the image to display on other parts of my site, so I guess it gives me options to expand that way, and it was interesting to play around with.

I had been considering adding a [geo kit gem](http://www.rubygeocoder.com/) to plot users and venues on a map and show the closest venues, but I'm feeling ready to do some final refactoring and submit this project. This would be another option to expand it in the future. 

