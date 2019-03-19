---
layout: post
title:      "The Goings On (React/Redux/Rails)"
date:       2019-03-19 23:02:46 +0000
permalink:  the_goings_on_react_redux_rails
---


Flatiron school finish line! Yet another thing I love about coding is that “YES” feeling when you figure something out that at first seemed impossible. Creating a web app with a React/Redux frontend and a Rails API backend? YES!

Here's the [Github Repo](https://github.com/MFeuer23/the-goings-on)
and the [Walkthrough Video](https://youtu.be/jDQ6VV73Kus)

### Initial Setup

So at first I spent some time researching how I get my React/Redux front end to communicate with my Rails API. [This blog post](https://www.fullstackreact.com/articles/how-to-get-create-react-app-to-work-with-your-rails-api/) proved to be very helpful. I followed along and set up my initial file structure using `create react app` and `rails new project-name-here --api --database=postgresql` I wanted to use postgresql because (spoiler alert) I’m considering deploying this project to Heroku! Maybe I’ll do another blog post about that. This was my first time using postgres, and it is really pretty similar to sqlite3.

Next, I set up foreman to boot up both my web app and my API by including `gem ‘foreman’, ‘~> 0.82.0’`, running `bundle install`, creating a Procfile where I declared my two processes:
```
web: cd client && npm start
api: bundle exec rails s -p 3001
```
And then I added a rake task so that instead of runing `foreman start -p 3000`, I could just run `rake start` to boot up the whole thing (how nice!)
```
task :start do
	exec ‘foreman start -p 3000’
End
```
Also interesting to note- since my React/Redux front ent is on localhost/3000, and my Rails API is on localhost/3001, there are some concerns with CORS (cross-origin resource sharing). So inside my package.json file, I had to set up a proxy by adding the line: `”proxy”: “http://localhost:3001”,` and in my redux actions where my fetch requests take place, I had to add `{accept: 'application/json',}` to specify.

### Using the NASA API

From there, I gave a lot of thought to integrating with an API and after playing around with a bunch of different APIs on Postman, I decided on [NASA’s EONET API](https://eonet.sci.gsfc.nasa.gov/docs/v2.1). They have such great documentation, and a really cool functional web app, [Worldview](https://worldview.earthdata.nasa.gov/) that shows natural events based on different parameters.
The next challenge was to make call to the NASA EONET API in my rails controller. I used [RestClient ruby gem](https://github.com/rest-client/rest-client) and specified different urls based on params I passed to the controller via my redux action fetch request.
```
class EventsController < ApplicationController

 def events
   if params[:category] == "all"
     url = "https://eonet.sci.gsfc.nasa.gov/api/v2.1/events?limit=4&status=#{params[:status]}"
   else
     url = "https://eonet.sci.gsfc.nasa.gov/api/v2.1/categories/#{params[:category]}?limit=4&status=#{params[:status]}"
   end

   begin
   response = RestClient::Request.execute(
     method: :get,
     url: "#{url}",
   )

   rescue RestClient::ExceptionWithResponse => e
   puts e.response
   end
   render json: response
 end
end
```

### The Flow

Then, next I had to figure out how to trigger this API call from my client side. First I created my redux store in the index.js file: `const store = createStore(rootReducer, applyMiddleware(thunk))` and passed it to my Provider: `<Provider store={store}>` so I’d be able to connect the store to various components and use `MapStateToProps` and `MapDispatchToProps`. Then in my EventsContainer Component, I made an EventsForm Component. EventsContainer is connected to the store, so it can dispatch actions. I imported the `fetchEvents` action and passed it to the EventsForm Component as a prop. This way the EventsForm would be able to call the dispatch action without also having to be connected to the redux store.

```
import EventsForm from '../components/EventsForm'
import { connect } from 'react-redux'
import { fetchEvents } from '../actions/EventActions'

class EventsContainer extends Component {

...

  render(){
     return(
       <div>
         <EventsForm fetchEvents={this.props.fetchEvents} />

         ...
				
			 </div>
   )
 }
}

...

export default connect(mapStateToProps, {fetchEvents})(EventsContainer);
```


Meanwhile, over at /client/src/actions/EventActions.js:

```
export function fetchEvents(state) {
 return (dispatch) => {
   dispatch({ type: 'LOADING_EVENTS', state});
   return fetch(`events/${state.category}/${state.status}`, {accept: 'application/json',})
     .then(response => {return response.json() })
     .then(responseJSON => {return responseJSON})
     .then(payload => dispatch({ type: 'FETCH_EVENTS', payload }));
 };
}

```
So I’m taking the state object that we capture from EventsForm OnSubmit, and passing it to my EventsController as params.

First it dispatches a promise to the EventsReducer: `dispatch({ type: 'LOADING_EVENTS', state});`

Then it dispatches an action with the response from the NASA API: `.then(payload => dispatch({ type: 'FETCH_EVENTS', payload })`

A quick note about redux-thunk middleware: it allows us to use functions (like fetch) in our actions. I used it by importing it into my Index.js component and calling `applyMiddleware` when I created the redux store.

```
import thunk from 'redux-thunk';

const store = createStore(rootReducer, applyMiddleware(thunk))
```

The eventsReducer looks like this:

```
function eventsReducer(state = {
 loading: false,
 data: []
}, action) {
 switch (action.type) {
   case "LOADING_EVENTS":
     return {...state, loading: true}
   case "FETCH_EVENTS":
     return { loading: false, data: action.payload}
   default:
     return state
 }
}
export default eventsReducer;
```

This returns the state object that is the redux store, allowing us to access the payload by connecting to the store and mapping state to props! This is the general flow of this application:  

`Action (promise) -> Rails Controller -> Action (reponse) -> Reducer -> Updated State`

### Using React-Bootstrap

Next I started thinking about how I was going to display the data that was returned from the NASA API. I used [React-Bootstrap](https://react-bootstrap.github.io/) to style the whole thing, and wound up using the CardDeck and Card components to display each event. It was remarkably simple to set up. First I installed Bootstrap by entering into the terminal:

`npm install react-bootstrap bootstrap`


Then, I added the following link tag to my App.js component:
```
<link
  rel="stylesheet"
  href="https://maxcdn.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css"
  integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS"
  crossorigin="anonymous"
/>
```

Then I was good to go! From there, you can import any of the Bootstrap Components into any React Component. For Example, from my EventsContainer Component:
```
import Card from 'react-bootstrap/Card'

...

const Event = props => {
  console.log(props)

  return(
    <div>
      <Card bg="secondary" style={{ width: '14rem' }}>

        <Card.Body>
          <Card.Title>
            <input defaultChecked={props.chk}
               id="check" type="checkbox" className="fa fa-star"></input>
       
            {props.info.title}
          </Card.Title>

          <Card.Subtitle>{props.info.description}</Card.Subtitle>
   
          <Card.Link target="_blank" style={{ color: '#8b0000' }} href={props.info.sources[0].url}>Source Link</Card.Link>

          <Card.Footer>{props.info.categories[0].title}</Card.Footer>
        </Card.Body>

      </Card>
      <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css" />
    </div>
  )

}

export default Event;
```

I also used a stylesheet from Font-Awesome to style a checkbox to look like a star, which brings me to the next section.

### Favorites

So once the Events were all displaying correctly, I wanted to add a Favorite feature to persist starred events to the database. I created a star-shaped checkbox on each Event card. I passed it a prop from EventsContainer that allows it to trigger a function called toggleStar:

```
onClick={(event) => props.toggleStar(
              event,
              props.info.title,
              props.info.description,
              props.info.categories[0].title,
              props.info.sources[0].url
            )}
```

toggleStar checks if the star checkmark is checked or not. If it is checked, it saves it to the Favorites table in the database. If it is not checked, it deletes it from the database.

From EventsContainer:

```
  toggleStar = (event, title, description, category, source_url) => {
    if (event.target.checked) {
      this.props.createFavorite(title, description, category, source_url)
    } else {
      this.props.deleteFavorite(
        this.props.favorites.find(favorite => favorite.title === title).id
      )
    }
  }
```

So `createFavorite` and `deleteFavorite` are imported form FavoriteActions, and when called, they dispatch different arguments to the FavoritesController as params. There is also a `fetchFavorites` action that returns all of the favorites that are stored in the database currently. I have fetchFavorites in my App.js Component in the ComponentDidMount function, so it's triggered immediately when the App is loaded, and it's stored in the redux store. This way, both EventsContainer and FavoritesContainer can have access to the data. EventsContainer needs to call on Favorites in order to delete a favorite. I also created an algorithm to compare Events and Favorites so that if an Event that was already Favorited is fetched again from the NASA API, its star-checkbox defaultChecked attribute will be set to `true` when it is rendered.

```
  faves = (arr1, arr2) => {
    if (arr1 && arr2) {
      let eArray = arr1.map(x => x.title)
      let fArray = arr2.map(x => x.title)
      let matches = [];
      for(let i in eArray) {
          if(fArray.indexOf(eArray[i]) > -1){
              matches.push(eArray[i]);
          }
      }
      return matches;
    }
  };
```

Right now, `faves` is being called in the EventsContainer and conditionally sets the `chk` prop in the Event Components to true or false depending on whether or not the Event already exists in the Favorites table.


From FavoriteActions:

```
export function createFavorite(title, description, category, source_url) {
  return dispatch => {
    return fetch('favorites', {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ title, description, category, source_url})
    })
      .then(response => {return response.json() })
      .then(responseJSON => {return responseJSON})
      .then(payload => {
        dispatch({ type: 'CREATE_FAVORITE', payload });
      }).catch(function(error) {
    console.log(error)
  })
  };
}

export function fetchFavorites() {
  return (dispatch) => {
    dispatch({ type: 'LOADING_FAVORITES' });
    return fetch('favorites', {accept: 'application/json',})
      .then(response => {return response.json() })
      .then(responseJSON => {return responseJSON})
      .then(payload => dispatch({ type: 'FETCH_FAVORITES', payload }));
  };
}

export function deleteFavorite(favoriteId) {
    return (dispatch) => {
        return fetch(`favorites/${favoriteId}`, {
            method: 'DELETE',
            headers: { 'Content-Type': 'application/json'}
        })
        .then(response => response.json())
        .then(payload => dispatch({ type: "DELETE_FAVORITE", payload}))
    }
}
```

`createFavorite` sends a post request to `'favorites'`,  `deleteFavorite` sends a delete request to `'favorites/:id'`, and `fetchFavorites` sends a get request to `'/favorites'`. All of them go to the FavoritesController, which communicate with the database and return JSON  to the action, which then dispatches an action to the reducer.

Here's the FavoriesController:

```
class FavoritesController < ApplicationController

  def index
    render :json => Favorite.all
  end

  def create
    @favorite = Favorite.find_or_create_by(
      title: params["title"],
      description: params["description"],
      source_url: params["source_url"],
      category: params["category"]
    )
    render json: @favorite
  end

  def destroy
    @favorite = Favorite.find(params[:id])
    @favorite.destroy
    render json: @favorite
  end

end
```

and the favoritesReducer, which updates the redux store:

```
function eventsReducer(state = {
  loading: false,
  favoritesData: []

}, action) {
  switch (action.type) {
    case "CREATE_FAVORITE":
      console.log(action)
      return {...state, favoritesData: [...state.favoritesData, action.payload]}

    case "LOADING_FAVORITES":
      console.log(action)
      return {...state, loading: true, favoritesData:[], }

    case "FETCH_FAVORITES":
      console.log(action)
      return {...state, loading:false, favoritesData: action.payload}

    case "DELETE_FAVORITE":
      console.log(action)
      return {...state, loading: false,
      favoritesData: state.favoritesData.filter(
        favorite => favorite.id !== action.payload.id
        )
      }

    default:
      return state
  }
}

export default eventsReducer;
```

The FavoritesContainer works similiarly to display the data as Favorite cards, using Bootstrap. The defaultChecked attribute of each star-checkbox is automatically set to `true` when it is rendered, and it dispatches `deleteFavorite` onClick.

### React Router

I also used React Router to simulate different routes for my main page, About page, and Favorites page. 

This is my App component:

```
import React, { Component } from 'react';
import { connect } from 'react-redux'
import './App.css';

import EventsContainer from './containers/EventsContainer'
import FavoritesContainer from './containers/FavoritesContainer'
import NavBar from './components/Navbar'
import About from './components/About'
import Header from './components/HeaderImg'

import { BrowserRouter as Router, Route, Link } from "react-router-dom";
import { fetchFavorites } from './actions/FavoriteActions'


class App extends Component {
  componentDidMount = () => {
      this.props.fetchFavorites();
  }

  render() {
    return (
      <Router>
        <div className="App App-body">
          <Header />
          <NavBar />
          <br/>
          <Route exact path="/" component={EventsContainer}/>
          <Route exact path="/favorites" component={FavoritesContainer}/>
          <Route exact path="/about" component={About}/>
          <link
            rel="stylesheet"
            href="https://maxcdn.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css"
            integrity="sha384-GJzZqFGwb1QTTN6wy59ffF1BuGJpLSa9DkKMp0DgiMDm4iYMj70gZWKYbI706tWS"
            crossOrigin="anonymous"
          />
        </div>
      </Router>
    );
  }
}

export default connect(null, { fetchFavorites })(App);
```

Another approach would have been to use MapStateToProps in the App component and pass props within the Main and Favorites routes. In order to render a component with props within a route, you can use `render` instead of `component`. For example:

```
  <Route exact path="/" render={() => <EventsContainer events={this.props.events}/>}/>
```


### Finishing Up

From here, there is just further refactoring and styling! Thank you so much for reading.


