---
layout: post
title:      "Deploy React, Redux & Rails App with Heroku"
date:       2019-04-04 14:47:24 +0000
permalink:  deploy_react_redux_and_rails_app_with_heroku
---


At first I wasn't sure how to begin tackling deploying an app to the internet with Heroku. Like most things, I realized it was a lot easier than I imagined it would be! The feeling when I got it to work was pretty amazing, and well-worth the effort. Here's [my app](http://the-goings-on.herokuapp.com/) on the actual internet! It's called The Goings On. Here's the [GitHub Repo](https://github.com/MFeuer23/the-goings-on) if you wanna see what I did under the hood.

I read a lot of different blogs that helped me gain a better understanding of the task at hand (and contributed to my confusion).  Using PostgreSQL for the database is important. I knew that going in, so I created my app with pg from the start to avoid headaches later on (although it seems doable to switch from sqlite3 to postgreSQL after the fact too). At some point I thought I also had to switch from npm to yarn for my front end dependencies, but I ultimately got it to work more easily with npm! I thought I would need to have separate GitHub repositories for my React/Redux front end, and my Rails API backend, but I got it working pretty easily with just one repo.


The blog post that helped the most was [this one](https://medium.com/superhighfives/a-top-shelf-web-stack-rails-5-api-activeadmin-create-react-app-de5481b7ec0b). I skipped the stuff about ActiveAdmin, but the part about preparing your app for production was super helpful.

First I made an account on the Heroku website, and created the app called 'the-goings-on.' You can also create an app from your terminal, using the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) if you like.

## Heroku CLI
Next, download and install the Heroku CLI. I use Ubuntu, so I just ran `sudo snap install --classic heroku` in my terminal. 

You can verify that the installation worked by executing in your terminal `heroku --version`. It should return the heroku version (if it worked).

Then `cd` into the root directory of your project, and run `heroku login`. This should work automatically if you are logged in to Heroku. Otherwise, you can log in manually in the terminal by running `heroku login -i`.

We also have to set up our Heroku git remote by executing `Heroku git:remote -a PROJECT-NAME` in terminal. So for my project, I entered: `Heroku git:remote -a the-goings-on`.

Then to attempt deployment, all we need to enter is `git push heroku master`, BUT FIRST we have to prepare our app for production.

## package.json
One thing that tripped me up a bit was the fact I needed to add a package.json file to my root repository, keeping the original package.json file that was already in my client folder. So there will be 2 package.json files!

**client/package.json**
```
{
  "name": "the-goings-on",
  "version": "0.1.0",
  "private": true,
  "proxy": "http://localhost:3001/",
  "dependencies": {
    "bootstrap": "^4.3.1",
    "react": "^16.8.3",
    "react-bootstrap": "^1.0.0-beta.5",
    "react-dom": "^16.8.3",
    "react-redux": "^5.1.0",
    "react-router-dom": "^4.3.1",
    "react-scripts": "2.1.5",
    "redux": "^4.0.1",
    "redux-thunk": "^2.3.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": [
    ">0.2%",
    "not dead",
    "not ie <= 11",
    "not op_mini all"
  ]
}

```


**package.json (root directory)**
```
{
  "name": "the-goings-on",
  "engines": {
    "node": "8.15.1"
  },
  "scripts": {
    "build": "cd client && npm install && npm run build && cd ..",
    "deploy": "cp -a client/build/. public/",
    "postinstall": "npm run build && npm run deploy && echo 'Client built!'"
  }
}
```

Having a `package.json` file in the root directory of the app is necessary to tell Heroku that we are using React. Interesting to note that we're using `package.json`'s `postinstall` to deploy a Rails server with our React/Redux client side in the `/public` directory.

First I thought I had to combine both of these into one `package.json` file in the root directory. No, don't do that. (Cue all the errors)


## Heroku Buildpacks
With my `package.json` files set up, Heroku can start to recognize that I have a dual Rails and Node app. [Buildpacks](https://devcenter.heroku.com/articles/buildpacks) will help us communicate to Heroku that we want to use two build processes in a specific order. First we want to use node to manage the front end, and then ruby for the Rails API. We can accomplish this by entering this command into the your terminal (if and when you have the Heroku CLI set up):
```
heroku buildpacks:add heroku/nodejs --index 1
heroku buildpacks:add heroku/ruby --index 2
```

## Foreman and the Procfile
I already had Foreman set up to run my React and Rails servers locally. This is a great [blog post](https://www.fullstackreact.com/articles/how-to-get-create-react-app-to-work-with-your-rails-api/) for getting this going. You can also check out my previous [blog post](http://malloryfeuer.net/the_goings_on_react_redux_rails), which goes through how I set it up.

Now assuming you've got a working development rake task and Procfile, here's how to prepare it for production.

Take your current root directory Procfile and rename it `Procfile.dev`:

**Procfile.dev**
```
web: cd client && PORT=3000 npm start
api: PORT=3001 && bundle exec rails s
```

Make a new Procfile in the root directory:

**Procfile**
```
web: bundle exec rails s
```

Then, we want to specify different rake tasks for development and production:

**/lib/tasks/start.rake**
```
namespace :start do
  task :development do
    exec 'foreman start -f Procfile.dev'
  end

  desc 'Start production server'
  task :production do
    exec 'NPM_CONFIG_PRODUCTION=true npm run postinstall && foreman start'
  end
end

desc 'Start development server'
task :start => 'start:development'
```

## git push heroku master
So with these changes, I was able to get it deployed. I went through many error messages and attempts, but these were those golden steps that worked it all out. One other note- the first error I experienced was about my ruby version. I had to use `rvm` to change my ruby version to one that was supported on Heroku. So you can just enter in your terminal:

```
rvm install 2.2.5
rvm use 2.2.5
```

2.2.5 is currently supported. More on this [here](https://devcenter.heroku.com/articles/getting-started-with-ruby)

## Celebrate
![](https://media.giphy.com/media/l46CimW38a7TFxLVe/giphy.gif)

Did it!! I hope this was helpful, and thanks for reading.






