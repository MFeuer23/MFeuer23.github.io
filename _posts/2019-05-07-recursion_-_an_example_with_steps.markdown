---
layout: post
title:      "Recursion - an example with steps"
date:       2019-05-07 17:36:32 +0000
permalink:  recursion_-_an_example_with_steps
---


Recursion is the concept of a function calling itself either directly or indirectly. To illustrate some important points about recursion, let's look at how we would refactor an iterative solution to become a recursive one.

Let's first develop an interative solution to a `steps` problem. The directions state that we must write a function that will console log a step shape with `n` levels using the `#` character. 

**Examples:**
```
steps(2)
'# '
'##'
	 
steps(3)
'#  '
'## '
'###'
```

So the solution depends on finding a pattern that determines when we should add a `#` and when we should add a blank space to our `stair` string. Making a diagram can help us to visualize the pattern here: if the column number is less than or equal to the row number, we should add a `#`, else just a space.

| n = 3 | Column 0 | Column 1 | Column 2 |
| -------- | -------- | -------- | -------- |
| Row 0 | # | - | - |
| Row 1 | # | # | - |
| Row 2 | # | # | # |

**iterative solution:**
```
function steps(n) {
   for (let row = 0; row < n; row++) {
	    let stair = ' ';
			
			for (let column = 0; column < n; column++){
			   if (column <= row) {
			      stair += '#';
			   } else {
				    stair += ' ';
				 }
			}  
			
			console.log(stair);
	 }
}
```

**A few general  points on recursion before we refactor this iterative solution to be recursive:**
* The very first thing we should do when developing a recursive solution is to come up with a **base case**. This describes the condition when there is no more work for the function to do, and it's time to return and stop the recursion process. Getting the base case established early on will allow us to run and test our solution without creating an infinite loop.
* We want to call our function again with different arguments, the **recursive step**. It is very important to make sure we have changed the arguments in some way on each function call, and that this change will lead us to the base case. If we do not change the arguments, we'll never reach the base case, and it'll cause an infinite loop.
* We also have to think about **reasonable defaults**. If there are any optional inputs, we should give them default values so that we can safely handle calling our function with our without these arguments without entering an infinite loop.

##### Refactor!
So now, in order to refactor our iterative solution, the first step is identifying our base case. If `row === n`, this means we are at the end of our problem. This is something we noticed in our iterative solution as well since we iterated through each row up to `n` in our for loop. 

It is also important to think about some other patterns from our iterative solution. We can note that if the `stair` string has a `length === n`, then we are at the end of a row, and we're ready to console log. If the length of the `stair` string is less than or equal to the row number we're working on, we add a `#` , otherwise add a space.

```
function steps(n) {
   if (n === row) {  // base case
	    return;
	 }
}
```

So here is our base case as we described earlier. However, right now there is no variable or argument called `row`. We can add `row` as an argument to our steps function to specify the current row we are on. We also need to make sure (as we mentioned earlier) that we give `row` a default argument, just in case it is not defined in our function call. Otherwise, we might get some really unexpected behavior. We can default the value of `row` to `0` so we will start on the very first row.

```
function steps(n, row = 0) {
   if (n === row) {  // base case
	    return;
	 }
}
```

Then we have to write some code to see if the length of the `stair` string is equal to `n`, and if it is, we console log out the value of `stair`.

```
function steps(n, row = 0) {
   if (n === row) {  // base case
	    return;
	 }
	 
	 if (n === stair.length) {  // if we are at the end of the row
	    console.log(stair)
	 }
}
```

However, like the base case, we don't have a variable called `stair` right now. So we can also add `stair` as an argument as well because it's another piece of information it would be useful to have with each function call. Again, we want to give it a default value to make sure we protect ourselves from calling the function with an undefined argument. The default value we'll give `stair` in this case will be an empty string `''`, which represents the beginning of the stair.

```
function steps(n, row = 0, stair = '') {
   if (n === row) {  // base case
	    return;
	 }
	 
	 if (n === stair.length) {  // if we are at the end of the row
	    console.log(stair)
	 }
}
```

Now, the only thing we have left to figure out is how we want to call our steps function again, and how we will make changes to `stair` and `row` so we will eventually reach our base case. One thing we know: we need to increment `row` when we reach the end of a row--like when we complete a stair!

```
function steps(n, row = 0, stair = '') {
   if (n === row) {  // base case
	    return;
	 }
	 
	 if (n === stair.length) {  // if we are at the end of the row
	    console.log(stair);
			steps(n, row + 1)  // increment row
			return;
	 }
}
```

So here we are calling our steps function again with `row + 1` to increment our `row` variable, but we are not passing it any value for `stair`. Here we are relying on our default value for `stair`, which is an empty string. At this stage, when we have just finished a row, and we console logged out the stair value, we want `stair` to reset to an empty string with which we can start our next row. Since there is nothing left to do here, we can return.

Now that we've handled our base case, and the case where we've hit the end of a row, what's left is to handle the case where we are still assembling our `stair` string. This is where we use the pattern that if the length of `stair` is less than or equal to the value of `row`, we add `#`. Otherwise add a space.

```
function steps(n, row = 0, stair = '') {
   if (n === row) {  // base case
	    return;
	 }
	 
	 if (n === stair.length) {  // if we are at the end of the row
	    console.log(stair);
			steps(n, row + 1)  // increment row
			return;
	 }
	 
	 if (stair.length <= row) {
	    stair += '#';
	 } else {
	    stair += ' ';
	 }
}
```

So here we've added some logic to deal with building out each stair, but at this point we're only recursing when we hit the end of a row. We still need to recurse through the row as well. So after we have added a character (`#` or `' '`), then we need to call our steps function again. This time, we'll call our steps function with the current value of `row` because we are not changing rows, and we'll pass it our current value of `stair`. So with each function call, unless our value of `row` is equal to `n` (base case), or the length of `stair` is equal to `n` (end of a row), we will add a value onto `stair`, which will increase its length.

```
function steps(n, row = 0, stair = '') {
   if (n === row) {  // base case
	    return;
	 }
	 
	 if (n === stair.length) {  // if we are at the end of the row
	    console.log(stair);
			steps(n, row + 1)  // increment row
			return;
	 }
	 
	 if (stair.length <= row) {
	    stair += '#';
	 } else {
	    stair += ' ';
	 }
	 
	 steps(n, row, stair);
}
```

Phew! Ok, so here we have it. Thank you for reading, and I hope this was helpful.

![](https://media.giphy.com/media/yAOjunY81Trjy/giphy.gif)
