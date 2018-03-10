---
layout: post
title:      "Babys First CLI Gem!"
date:       2018-03-09 19:09:36 -0500
permalink:  babys_first_cli_gem
---


It's hard to believe that a little over a month ago I had no idea what I was doing, but now I've just completed my first CLI Gem that shows you the status of the MTA Service! It was really challenging to scrape the MTA site- I still have some issues, and as with any creative endeavor, how can you ever tell when it's done? After a good talk with my education coach, I decided to hit submit!

The first hurdle was setting up my Github repository and re-learning what it means to add and commit and push. I went back through some earlier labs and got it down. Added my spec md. Commit!

Then getting the gem framework up was remarkably easy with `bundle gem mta_service_status`
That gave me all my files and folders, including a console. The next hurdle was requiring all of my files in the right way to get the thing to run. I had to fumble around with the gemspec file to get Nokogiri and Pry working. I had to require 'open-uri.' Got it going, console works.

Then I made a sort of flow chart where I wrote the code I wish I had. (I wrote it out on a literal piece of paper beacuse it felt less intimidating. Yes, the movie Wargames was on in the background.)

Then, I got to it! Started with my CLI class so I could welcome the user, tell them the menu options, get user input, validate user input, and show different output depending... to see my walkthrough video: https://youtu.be/9NOLt9LLLYg
and here's my Github link: https://github.com/MFeuer23/CLI-data-gem-project

You can type "status" to get a general status of all the trains, or type a specific train line to get more detailed information. The more detailed information brought me to a different site for each line, which was a bit tedious. Scraping the detailed information was also difficult because they are not all formatted the same way. Also, when the MTA website refers to a train, it uses an icon. So I had to get the values of the icons, but it was hard to integrate that into the text. So in certain cases, it looks a bit messy, and it's not super clear which train they're talking about (mostly when there is a complex service change and there are detailed instructions on which trains to take). But in MOST cases, it works ok.

Now it's as easy as `./bin/mta-service-status`

GO!


