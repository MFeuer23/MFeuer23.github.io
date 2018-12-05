---
layout: post
title:      "Adding JS to My Rails Project "
date:       2018-12-05 18:48:53 +0000
permalink:  adding_js_to_my_rails_project
---


This project really helped me gain a better understanding of AJAX (Asynchronous JavaScript And XML) and using the asset pipeline in Ruby on Rails to incorporate different stacks in my project. It's very exciting how the idea of myself being a "Full Stack Developer" is starting to come into focus as the end of this program is in sight, and I'm building my confidence to tackle more in-depth concepts! (React, here I come!)

Here's a link to my [GitHub repo](https://github.com/MFeuer23/nyc-indie-show-list/tree/js) as a reference. I created a js branch off of my last Rails project: nyc-indie-show-list.

So first off, I started thinking about the requirements and how to set up my serializers. Step one was to add `gem 'active_model_serializers'` to my Gemfile and run `bundle install` in my terminal. Then, I used my handy rails command line generator to make my serializer files for my Artist, Show, and Venue models (`rails g serializer artist`, etc...). Then I edited each serializer to include the attributes I needed, and set up my serializer relationships to echo my model relationships:

* **show** `belongs_to :artist` and `belongs_to :venue`, 
* **artist** `has_many :shows` and `has_many :venues, through: :shows`, 
* **venue** `has_many :shows` and `has_many :artists, through: shows`

Next, I set up my js files in my asset pipeline. I added a new file to `app/assets/javascripts` for each AJAX feature, and required them all in my `app/assets/javascripts/application.js` file (for example: `//= require artist_shows_index`) . 


## Rendering an Index Page via JS and AMS

The first feature I worked on was rendering an index page via JavaScript and Active Model Serialization. I added a button to my Artist show page that a user can click to view that artist's shows: `<button class="js-view-shows" data-id="<%= @artist.id %>">View Shows</button><br>`
I added a class attribute so my artist_shows_index.js file would be able to find the right button, and I added a data-id attribute so the js file would be able to access the current artist's id attribute. I also added two divs with specific class attributes for injecting the AJAX responses into the DOM (Document Object Model), one for upcoming shows, and one for past shows depending on the date.

One interesting issue I had was with the `$.ready()` function. Because Rails uses [Turbolinks](https://guides.rubyonrails.org/working_with_javascript_in_rails.html#how-turbolinks-works), my `$.ready()` wasn't being triggered unless I refreshed the page. I had more success with `$(document).on('turbolinks:load', function ()`.

So then I triggered my AJAX get request when a user clicks on the View Shows button, and prevented defaults so no redirect or page refresh would happen, and I used my data-id attribute to get the current artist's index of shows with my AJAX request. I also had to update my Show controller to handle rendering in JSON when a request is made to the index action: `render json: @shows, status: 200`


Then, I iterated through the response data and printed the shows in the Upcoming or Past show div, depending on the date:

```
$(document).on('turbolinks:load', function () {
  $(".js-view-shows").on('click', function(e) {
    e.preventDefault();
    let id = $(this).data("id");
    jQuery.get("/artists/" + id + "/shows.json", function(data) {
      $(".js-shows-upcoming").html("<h4>" + "Upcoming Shows" + "</h4>")
      $(".js-shows-past").html("<h4>" + "Past Shows" + "</h4>")
      $.each(data, function(index, value) {
        let date = new Date(value["date"])
        if (date >= new Date()) {
          $(".js-shows-upcoming").append("<li>" + "<a href='/shows/" + value["id"] + "'>" + formatDate(date) + " at " + value["venue"]["name"] + "</a>" + "</li>");
        } else {
          $(".js-shows-past").append("<li>" + "<a href='/shows/" + value["id"] + "'>" + formatDate(date) + " at " + value["venue"]["name"] + "</a>" + "</li>");
        }
      })
    });
  });
});
```

I made a `formatDate()` function to format my dates, similar to how you can use `strftime()` in ruby:

```
function formatDate(date) {
  let monthNames = [
    "January", "February", "March",
    "April", "May", "June", "July",
    "August", "September", "October",
    "November", "December"
  ];

  let day = date.getUTCDate();
  let monthIndex = date.getUTCMonth();
  let year = date.getUTCFullYear();

  return monthNames[monthIndex] + ' ' + day + ', ' + year;
}
```


## Rendering a Show Page via JS and AMS

The next requirement was to render a show page via Js and Active Model Serialization. I added a Next button to each Show show page. Similarly to the previous example, I made a button on my Show show page with a class of "js-next" and a data-id attribute of the current show's id, along with some spans with different id attributes for injecting the AJAX response back into the DOM:

```
<button class="js-next" data-id="<%= @show.id %>">Next Show</button>
<h3><span id="artist_name"><%= @show.artist.name %></span>'s Show at <span id="venue_name"><%= @show.venue.name %></span></h3>
<p><b>Date: </b><span id="show_date"><%= @show.date.strftime("%B %d, %Y") %></span></p>
<p><b>Info: </b><span id="show_info"><%= @show.info %></span></p>
<p><b>Location: </b><span id="venue_address"><%= @show.venue.address %></span></p>
```

I wrote some JQuery to fire an AJAX get request to the next show in the database when the user clicks on the "Next" button. I updated the Show controller's show action to handle html and json requests:

```
 def show
    @show = Show.find(params[:id])
    respond_to do |format|
      format.html {render :show}
      format.json {render json: @show, status: 200}
    end
  end
```
	
I used the data-id attribute and add 1 to it before the get request is made. Then, after the response is injected into the DOM, I reset  the data-id attribute of the "Next" button to equal the id of the show currently being displayed, so it will continue to update and display the next show until there is no show with the id of the data-id + 1 in the database.


```
$(document).on('turbolinks:load', function () {
  $(".js-next").on("click", function(e) {
    e.preventDefault();
    let nextId = parseInt($(".js-next").attr("data-id")) + 1;
    $.get("/shows/" + nextId + ".json", function(data) {
      $("#artist_name").text(data["artist"]["name"])
      $("#venue_name").text(data["venue"]["name"])
      $("#show_date").text(formatDate(new Date(data["date"])))
      $("#show_info").text(data["info"])
      $("#venue_address").text(data["venue"]["address"])
      $("#show_specific_links").text("")
      // re-set the id to current on the link
      $(".js-next").attr("data-id", data["id"]);
    });
  });
});
```

## Rendering a 'has_many' relationship through JSON using JavaScript

The next requirement was to dynamically render on the page at least one 'has-many' relationship through JSON using JavaScript. I chose to render a list of venues an artist has played on the Artist show page, and then made a link by each venue in the list that renders a list of other show dates at that venue. Agan, I started with a button with class and data-id attributes, as well as a div for injecting the responses into the DOM:

```
<button class="js-view-venues" data-id="<%= @artist.id %>">View Venues Played</button><br>
<ul><div class="js-venues"></div></ul>
```

I wrote some JQuery to fire an AJAX get request when the button is clicked to the artist's list of venues. I also updated the Venue controller's index action to handle JSON rendering: `render json: @venues, status: 200`.  I created a function to iterate through the AJAX response and remove duplicate venues:

```
function removeDuplicates(myArr, prop) {
    return myArr.filter((obj, pos, arr) => {
        return arr.map(mapObj => mapObj[prop]).indexOf(obj[prop]) === pos;
    });
}
```

And then appended each venue name along with a link to "See All Show Dates." I gave each link a data-id attribute, as well as a div that had a class with that same attribute dynamically assigned. This allowed me to iterate through my original response again to append a list of dates for the correct venue and append them in the correct place on the page.

```
$(document).on('turbolinks:load', function () {
  $(".js-view-venues").on('click', function(e) {
    e.preventDefault();
    let id = $(this).data("id");
    $.get("/artists/" + id + "/venues.json", function(data) {
      $(".js-venues").html("<h4>" + "Venues Played" + "</h4>")
      let dataUnique = removeDuplicates(data, "id")

      $.each(dataUnique, function(index, value) {
        $(".js-venues").append("<li>" + "<a href='/venues/" + value["id"] + "'>"
        + value["name"] + "</a>" + " // " + "<a href='#' class='js-show-dates' data-id='" + value["id"]
        + "'>See All Show Dates</a>" + "</li>");
        $(".js-venues").append("<ul><div class='" + value["id"] + "'></div></ul>")
      })

        $(".js-show-dates").on('click', function(e) {
          e.preventDefault();
          let venue_id = $(this).data("id");

          $.each(dataUnique, function(index, value) {
            if (value["id"] == venue_id) {

              $.each(value["shows"], function(index, value) {
                let date = new Date(value["date"])
                $("." + venue_id).append("<a href='/shows/" + value["id"] + "'><li>" + formatDate(date) + "</li></a>")
              })
            }
          })
        })
    });
  });
});
```

## Using my Rails Application and JavaScript to Render a Form For Creating a Resource That Submits Dynamically

The next requirement entailed rendering a form to submit a new resource dynamically through AJAX. I wound up making my Artist show page render a form to submit a new show. I entered a form and rendered partials directly on my Artist show page inside a div with a class of "js-form-render" and toggled showing and hiding with my "Post a New Show" button. My form deals with two different associated objects: Show and Venue. I gave my form a class attribute so I would be able to control its submission through jQuery.

app/artists/show.html.erb
```
<% if @artist == current_artist %>
  <button class="js-new-show">Post a New Show</button>
<% end %>

<div class="js-form-render">
  <h3>Post a New Show</h3>
    <div class="js-errors"></div>
  <%= render "application/display_errors", object: @show %>

  <%= form_for(@show, html: {id: "js-form"}) do |f| %>
    <%= render "shows/form", f: f %>
    <p>Or Create a New Venue</p>
    <%= fields_for :venue, @show.venue do |f| %>
      <%= render "venues/form", f: f %>
    <% end %>
    <%= f.submit %>
  <% end %>

</div>
```

app/assets/javascripts/new_show.js

```
$(document).on('turbolinks:load', function () {
  $(".js-form-render").hide();
  $(".js-new-show").on("click", function(e) {
    e.preventDefault();
    $(".js-form-render").toggle();
    });
  });
```

## Translating the JSON Responses into JavaScript Model Objects Using Either ES6 Class or Constructor Syntax With At Least One Method on the Prototype

For my final requirement of this project, I wound up making two different model objects to handle my JSON reponses from my AJAX post request: one for Show and one for Venue. My prototype method was an "appendToDom(class_name)" method on my Show object.

```
class Show {
    constructor(data) {
      this.date = data["date"]
      this.info = data["info"]
      this.artist = data["artist"]
      this.venue = data["venue"]
    }
    appendToDom(class_name) {
      $(`.${class_name}`).html(
        "<h3>Posted Show....</h3>" +
        "<p> Date: " + `${formatDate(new Date(this.date))}` + "</p>" +
        "<p> Info: " + `${this.info}` + "</p>" +
        "<p> Artist: " + `${this.artist.name}` + "</p>" +
        "<p> Venue: " + `${this.venue.name}` + "</p>" +
        "<p> Address: " + `${this.venue.address}` + "</p>" +
        "<p> Contact: " + `${this.venue.contact}` + "</p>"
      )
    }
  }

  class Venue {
    constructor(showData, venueData) {
      this.name = venueData["name"]
      this.address = venueData["address"]
      this.contact = venueData["contact"]
      this.artist = showData["artist"]
      this.show = showData
    }
  }
```

This made my AJAX post request logic flow more easily. I also included some crude validations for my form because my regular model validation errors didn't seem to render unless the page was refreshed. I think the general form rendering and validations would have been much easier if I was able to use `remote: true` but this worked to show the user that the form entry was not entered into the database, and asks them to try again with a link to re-load the page.
```
if (!showData["date"]) {$(".js-errors").append("No Date Entered. <a href='" + `${showData["artist"]["id"]}` + "'>Try Again.</a><br>")}
```

Then, I added some logic to handle the way the form was filled out. The New Show form gives the artist an option to choose an existing venue from a drop-down menu, or create a new venue. If there is no venue selected from the drop-down menu, a new Venue object is created and associated to our Show object. Then our show object is appended to the DOM.

```
$(document).on('turbolinks:load', function () {
  $("#js-form").on("submit", function(e) {
    e.preventDefault();

      let values = $(this).serialize();
      let jsShow;
      console.log(values);

      $.post('/shows.json', values, function(showData) {
        if (!showData["date"]) {$(".js-errors").append("No Date Entered. <a href='" + `${showData["artist"]["id"]}` + "'>Try Again.</a><br>")}
        if (!showData["venue"]["id"]) {
          $.post('/venues.json', values, function(venueData) {
            if (venueData["name"] == "") {$(".js-errors").append("No Venue Entered. <a href='" + `${showData["artist"]["id"]}` + "'>Try Again.</a>")}
            showData["venue"] = new Venue(showData, venueData)
            jsShow = new Show(showData)
            jsShow.appendToDom("js-posted-object")
          })
        } else {
          jsShow = new Show(showData)
          console.log(jsShow)
          jsShow.appendToDom("js-posted-object")
        }
      });
    });
  });
```

To handle this in the Show and Venue controllers, I added respond_to blocks to handle html or json formats depending on the situation in which the create action is called. This enables my AJAX form to work, while still allowing my Venues New view to work as it did perviously. It also allows the params from my AJAX form to be added to the database on submission (as long as it passes the appropriate model validations).


app/controllers/venues_controller.rb
```
  def create
    respond_to do |format|
      @venue = Venue.new(venue_params)
      format.json {render json: @venue, status: 201}
      if @venue.save
        format.html {render :show}
      else
        format.html {render :new}
      end
    end
  end
```

app/controllers/shows_controller.rb
``` 
def create
    respond_to do |format|
      @show = Show.new(show_params)
      format.json {render json: @show, status: 201}
      @show.venue = Venue.create(venue_params) unless @show.venue_id

      if @show.save
        format.html {render :show}
      else
        format.html {render :new}
      end
    end
  end
	```
	
	
So with a bit more refactoring, I now have a functional Ruby on Rails web application that utilizes Javascript! Excited to move on to my last two lessons and my final project. Stay tuned, and thanks for reading.



