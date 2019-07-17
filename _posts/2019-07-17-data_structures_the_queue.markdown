---
layout: post
title:      "Data Structures: The Queue"
date:       2019-07-17 18:02:16 +0000
permalink:  data_structures_the_queue
---


A queue can be conceptualized as a container where records enter one end end and exit on the other. A queue is very much like waiting in line to buy tickets, except there is no skipping or cutting in the line. The order in which records are added to a queue dictates the order in which records can come out. Adding a record to a queue is called **enqueuing** and removing a record is called **dequeuing**.

A queue followd a First In First Out (FIFO) principle.

In JavaScript, there is really no such thing as a rudimentary queue. We have JS arrays, which have a lot of different built-in methods attached to them. 

In JS, in order to implement a queue, we can use array methods: 
* `array.unshift();` to add an item to the beginning of an array, or add to the queue
* `array.pop();` to remove an item from the end of an array, or remove from the queue

So we might make a queue class and initialize an empty array and only expose the unshift and pop methods outside of the class, to make it clear we are implementing a queue data structure. So we are essentially hiding access to all other built-in array methods that we will not need to utilize with the queue.

Why do this? We don't want to open up our queue to being treated like an array. If we are using a queue for a specific reason, we don't want to be able to change the order of the elements, or use other array methods that might mess up the queue.

This is a typical implementation in JavaScript of a Queue:
```
class Queue {
  constructor() {
    this.data = [];
  }

  add(record) {
    this.data.unshift(record);
  }

  remove() {
    return this.data.pop();
  }
}
```

We will use an array to store our data, and implement add and remove methods. So first, we initialize an array and assign it to the instance of the queue that is created inside the constructor method. (Whenever we create a new instance of a class, the constructor function will automatically be called.)

Then we create add and remove methods, explicitly returning the record that is removed in the remove method.

Thanks for reading!


