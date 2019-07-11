---
layout: post
title:      "React Development vs. Production Builds"
date:       2019-07-11 01:53:01 +0000
permalink:  react_development_vs_production_builds
---


As I work on developing my band's website, [www.thegraspingstraws.com](http://www.thegraspingstraws.com/) one thing I started noticing once I pushed a first draft to heroku was that my React Dev Tools icon was giving me an error. I was using the development build of React instead of the production build. This inspired me to learn more about the differences between the development and production build, and to figure out how to ready my app for production.

[This blog post](https://medium.com/quick-code/deploying-production-build-of-react-app-to-heroku-2548d8bf6936) was helpful in showing how to set up my package.json file with react scripts. The [react production build documentation](https://reactjs.org/docs/optimizing-performance.html#create-react-app) was also helpful.

First, I ran `npm run build` since I used `create-react-app` with my project. The terminal instructed me to specify a homepage in my package.json file:

```
  "homepage": "http://www.thegraspingstraws.com/",
```
 
 The blog post showed the different scripts we can specify in the package.json file as well:
 
 ```
   "scripts": {
    "dev": "react-scripts start",
    "start": "serve -s build",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject",
    "heroku-postbuild": "npm run build"
  },
 ```
 
* **`dev`** starts the development server


* **`start`** serves the production build


* **`heroku-postbuild`** generates a production build. This command is executed automatically on Heroku's server after project dependencies are installed

Then, after running `git push heroku master`, no more error! That's all it takes to get a simple React app ready for production and deployment using heroku.

