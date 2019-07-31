---
layout: post
title:      "Trees: Breadth First vs. Depth First Traversal"
date:       2019-07-31 19:35:36 +0000
permalink:  trees_breadth_first_vs_depth_first_traversal
---


There are two main ways to traverse a tree data structure: Breadth First and Depth First. With Breadth First, we go through each row from left to right, and then go to the following row. With Depth First, we esentially go from top to bottom through all the nodes on the left side, and then the right. Here's a diagram to better illustrate the order the nodes are visited:

![](http://mishadoff.com/images/dfs/binary_tree_search.png)

So first lets implement a tree data structure by implementing a Node. A node carries references to all the child nodes underneath it, and we usually make use of an array to store all those references. We also want to implement add and remove functions inside our Node class to create a new node and add it to the current node's children, and to remove a particular node. In the context of a tree, we have to specify which node we want to be adding or removing elements from. 
  
```
class Node {
   constructor(data) {
	    this.data = data;
			this.children = [];
	 }
	 
	 add(data) {
	    this.children.push(new Node(data));
	 }
	 
	 remove(data) {
	    this.children = this.children.filter(node => node.data !== data)
	 }
}
```

So nodes within a tree are responsible for adding and removing nodes, while the Tree class takes care of traversal. We want to assign a root property to the tree to reference the absolute head of the tree. Then we can iterate through each node in either a breadth first or depth first order by creating an array and adding and removing nodes from it. See below. 

```
class Tree {
   constructor() {
	    this.root = null;
	 }
	 
	 traverseBF(fn) {  //we can pass it a function that will be run on each iteration
	    const arr = [this.root]
			while (arr.length) {
			   const node = arr.shift();
				 
				 arr.push(...node.children);
				 fn(node);
			}
	 }
	 
	 	 traverseDF(fn) {  
	    const arr = [this.root]
			while (arr.length) {
			   const node = arr.shift();
				 
				 arr.unshift(...node.children);
				 fn(node);
			}
	 }
}
```

Notice the implementations of BF and DF are very similar. The only difference is that we are pushing the elements into the end of the array with BF, and unshifting them into the start of the array for DF, resulting in a different ordering of the nodes.

Thanks for reading!







