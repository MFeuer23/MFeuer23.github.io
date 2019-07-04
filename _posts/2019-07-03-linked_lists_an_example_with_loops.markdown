---
layout: post
title:      "Linked Lists, an example with loops"
date:       2019-07-04 03:02:41 +0000
permalink:  linked_lists_an_example_with_loops
---


A linked list is a data structure that represents a sequence of nodes where each node points to the next. Linked lists can be singly linked or doubly linked. A doubly linked list gives each node pointers both to the next node and to the previous node in the sequence.

Unlike arrays, linked lists do not have constant time access to a particular node within the list by index. With a linked list, if you'd like to find the Nth element on the list, you would have to iterate over N elements to find it.

The benefit of using a linked list data type is that you can add and remove items from the beginning of the list in constant time without having to shift each element. In contrast, to add and element to the beginning of an array, we will have to iterate through the entire array and shift each index by one.

In terms of Javascript, this is what a linked list looks like this :
```
  let firstNode = ['a', 123]
  let secondNode = ['b', 132]
  let thirdNode = ['c', null]
 
  let collection = {0: firstNode, 123: secondNode, 132: thirdNode}
  let head = firstNode;
```

Let's think about how we would write  a function that would take the first node of a linked list as its input, and return `true` if the linked list loops (repeats a data value), and `false` if no values are repeated.

In order to iterate through a linked list data structure, we will need to either set up a `while` or `until` statement to keep iterating while `node.next` and none of the data values have repeated, or we can use recursion and set a base case.
