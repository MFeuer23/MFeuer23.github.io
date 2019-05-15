---
layout: post
title:      "Object Oriented JavaScript"
date:       2019-05-15 15:09:08 +0000
permalink:  object_oriented_javascript
---


Object Oriented Programming (OOP) uses abstraction to create objects based on the real world. OOP uses techniques such as **modularity**, **inheritance**, **polymorphism** and **encapsulation** to allow software to function as a collection of cooperating objects, rather than as a list of commands. This makes our code more flexible and easier to maintain.

I found [this documentation  from MDN](https://developer.mozilla.org/ms/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript) to be particularly helpful as an overview. 

### Namespace
Conceptionally, a namespace is a container with a specific name in which we can bundle up functionality. However in JavaScript, there's no language-level difference between namespaces and regular objects. To create a namespace in JavaScript, we can create one global object, and all variables, methods, and functions become its properties. 

For example, here's how we can create a global object called NAMESPACE:
```
let NAMESPACE = NAMESPACE || {};
```
This allows us to check whether NAMESPACE has already been defied. If so, use the existing global object, if not create an new empty object.

Here's an example of how we would add on a method:
```
NAMESPACE.commonMethod = {
  validateName: function(name){
    console.log(`hello ${name}`)
  },
```

### Built-In Objects
JavaScript has built-in objects such as Math, Object, Array and String. For example, we can use `Math.random()` to generate a random value between 0 and 1. Every object in JavaScript is an instance of the built in `Object` and inherits its properties and methods.

See more about the different types of built-in objects [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects).

### Inheritance
Inheritance is the concept of creating a child object as a specialized version of a parent object. Like when using React, we can import the React library and create classes that inherit functionality from React's Component class.

```
import React, { Component } from 'react'

class Events extends Component {

  render(){
    return(
      <div>
        <p>I have all the functionality of a React Component!</p>
      </div>
    )
  }

}
export default Events;
```
 ### Encapsulation
 Encapsulation is the concept of restricting direct access to some of the object's components. We want to enclose all of the functionality of an object within the object itself so that it's methods and properties are hidden from the rest of the application. This allows us to abstract or localize specific functionality in our objects and separate concerns.
 
 ### Polymorphism
Polymorphism is the concept that different classes can define methods with the same name because methods are scoped to the class in which they are defined (unless one object inherits from another)



Thanks for reading! 

![](https://media.giphy.com/media/XreQmk7ETCak0/giphy.gif)








