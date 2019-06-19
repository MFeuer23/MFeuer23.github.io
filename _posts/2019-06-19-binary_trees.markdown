---
layout: post
title:      "Binary Trees"
date:       2019-06-19 19:57:04 +0000
permalink:  binary_trees
---


Trees are a recursive data structure, like array and linked lists, but they connect nodes in a parent/child relationship. For example, every array is made up of smaller arrays. Similiarly, a tree is made up of smaller trees. If we were to remove a node from a tree, we would still have a tree.

A binary search tree is a specific type of tree where we organize the nodes such that no parent can have more than two children (binary), and the value of each left child node is less than its parent node and the value of each right child node is more than the parent node. 

The order in which we insert values into a binary search tree will change the way the tree looks. Also, a **directed tree** means that each parent node nolds a pointer to its children, but children do not know about their parent. 

Each node store three pieces of information: its own data, its right child node, and its left child node. This is what it looks like with JavaScript objects.
```
let node = {data: 4, rightChild: null, leftChild: null}
```
if we were to add more nodes to our tree
```
let binarySearchTree = 
	    {data: 6, 
		 rightChild: {data: 8, leftChild: null, rightChild: null},
		 leftChild: 
		    {data: 1,
		    rightChild: {data: 4, rightChild: null, leftChild: null},
			leftChild: null}
         };
```

## Inserting a New Node
If we want to write a function called `find` that, given a tree's root node and a new piece of data, it will place that data in the correct place on the tree, how do we approach this? 

One approach is to draw out a tree and/or think about it, and then translate it into code.

So if we wanted to insert 3 into the following tree,
```
          7
	 	/
		2
		 \
		  5
```
it becomes,
```
          7
	 	/
		2
		 \
		  5
		 /
    	3
```

We know we need to compare the number we would like to insert with the current node, starting at the root node. We look to the left or right, depending on whether the new node is smaller or larger. If there is no existing node, we insert the node there. Otherwise, we go through the process again. Sound a little bit like recursion? 

```
function findOrAdd(rootNode, newNode){
  let currentNode = rootNode
  if(newNode.data < rootNode.data){
      currentNode = rootNode.left
      if(currentNode){
        findOrAdd(currentNode, newNode)
      } else {
        rootNode.left = newNode
      }
  } else if(newNode.data > rootNode.data) {
    currentNode = rootNode.right
    if(currentNode){
      findOrAdd(currentNode, newNode)
    } else {
      rootNode.right = newNode
    }
  }
}
```

The recursive case is to compare `newNode` against `currentNode` and move to the right or left accordingly. The base case happens when after moving to the right or left, we do not find a node. In that case, we assign `newNode` to its position and stop.

## Printing Nodes in Order
Next how would we write an `inOrder` method that, given a root node, console.logs all of the other nodes in a tree, in sequential order. How do we do this? First, come up with an example:
```
          7
	 	/ \
		2   9
		 \
		  5
		 / \
    	3   6
```

We know that in a binary search tree, everything to the left of a node is smaller than everything to the right. What if we break down our tree a bit?
```
          7
	 	/ \
		2   9
```
Here, we can clearly see we would take the branch to the left, then the center node, then the branch to the right. With our more complex tree above, how do we get to the other values? Recursion again? yes!

We can consider `inOrder` to be this approach of taking the left child, center node, then the branch to the right. We just have to figure out the base case and recursive case.

In our example, `inOrder` sees that our root node has a left child of 2.  We then call `inOrder` again with 2 as the center node. 2 does not have a left child, so we print it out, then we move to its right child, and we call `inOrder` again on the right child. So we would have 2, then 3, then, 5, then 6. The left subtree is complete! Our base case is when we reach nodes that have no children. So then we move onto the right subtree, and we print it `inOrder`, which is just 9.

```
function inOrder(currentNode){
  if(currentNode.left){
    inOrder(currentNode.left)
  }
  console.log(currentNode.data)
  if(currentNode.right){
    inOrder(currentNode.right)
  }
}
```

![](https://media.giphy.com/media/L15K9eX1d86A0/giphy.gif)

Thanks for reading!!

