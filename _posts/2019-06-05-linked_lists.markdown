---
layout: post
title:      "Linked Lists"
date:       2019-06-05 14:49:16 +0000
permalink:  linked_lists
---


A Singly Linked List is like a scavenger hunt. The benefit of using Linked Lists is that there is constant insertion time `O(1)`. Access, search and deletion time is all linear `O(n)`. Here's a table of how Linked Lists compare to other Data Structures in terms of time complexity.

| Data Structure | Access | Search | Insertion | Deletion | Special Cases |
| -------- | -------- | -------- | -------- | -------- | -------- |
| **Array** | 1 | n | n | n |   |
| **Stack** | n | n | 1 | 1 |   |
| **Queue** | n | n | 1 | 1 |   |
| **Linked List** | n | n | 1 | n |   |
| **Hash Table** | - | n | n | n | In case of perfect hash function, costs would be O(1) |
| **Binary Search Tree** | n | n | n | n | in case of balanced tree, costs would be O(log(n)) |

## **Build a Constructor Function for a Singular Node**
First, we want to build a constructor function for a singular Node: `new Node(value)`. A node has attributes for `value` and `next`.

```
class Node {

  constructor(value) {
    this.value = value;
    this.next = null;
  }

}
```

## **Build a Constructor Function for a Singly Linked List**
Then we want to build a constructor function for a singly linked list: `new SinglyLinkedList()`. A singly linked list has attributes for `head` and `length`. We can `add` values that build new nodes, we can `search` the list for a node at a specific position, and we can `delete` a node from a specific position.

```
class SinglyLinkedList {

  constructor() {
    this._length = 0;
    this.head = null;
  }

  add(value) {
    const node = new Node(value);
    let currentNode = this.head;

    // is the list empty?
    if (!currentNode) {
      this.head = node;
      this._length++;

      return node;
    }

    // is the list not empty?
    while (currentNode.next) {
      currentNode = currentNode.next;
    }

    currentNode.next = node;
    this._length++;

    return node;
  }

  searchNodeAt(position) {
    let currentNode = this.head;

    // the position isn't valid? is the length of the list 0? is the position less than 1? or is the position greater than the length of the list? 
    this.isValidPosition(position)

    // the position is valid?
    for (let i = 1; i < position; i++) {
      currentNode = currentNode.next;
    }

    return currentNode;
  }

  removeNodeAt(position) {
    let currentNode = this.head;
    let beforeNodeToBeDeleted = null;
    let possiblyDeletedNode = null;
    let deletedNode = null;

    // the position isn't valid?
    this.isValidPosition(position)

    // are we removing the head node?
    if (position === 1) {
      this.head = currentNode.next
      deletedNode = currentNode;
      this._length--;

      return deletedNode;
    }

    // are we removing a node from the tail?
    for (let i = 1; i < position; i++) {
      // store the value of the currentNode to the beforeNodeToBeDeleted
      beforeNodeToBeDeleted = currentNode;
      // assume that the next node in the iterator could be deleted
      possiblyDeletedNode = currentNode.next; // { value: 11, next: { value: 12, next: null }}
      currentNode = currentNode.next;
    }

    beforeNodeToBeDeleted.next = possiblyDeletedNode.next;
    deletedNode = possiblyDeletedNode;
    this._length--;

    return deletedNode
  }

  isValidPosition(position) {
    if (this._length === 0 || position < 1 || position > this._length) {
      throw new Error("Unable to find a node at this position")
    }
  }
}
```

Then, in order use our constructor functions:

```let list = new SinglyLinkedList()``` gives us:
```
SinglyLinkedList {_length: 0, head: null}
```

then if we added some nodes...

```
list.add(10)
list.add(11)
```

our Linked List will look like:

```
SinglyLinkedList{_length: 2, head: Node {
   value: 10, next: Node {
	    value 11, next: null
		}
	}
}
```

then if we wanted to delete a node...

```
console.log("Removed Node: ", list.removeNodeAt(2)) 
```
returns:
```
Removed Node: Node {value: 11, next: null}
```

and then our Linked List looks like:

```
SinglyLinkedList{_length: 1, head: Node {
   value: 10, next: null
	}
}
```

Thanks for reading!! Enjoy this educational Linked List gif (we can also reverse linked lists.. there are doubly linked lists. THE POSSIBILITIES!)

 ![](https://media.giphy.com/media/12tY7kkS2b6FQ4/giphy.gif)

