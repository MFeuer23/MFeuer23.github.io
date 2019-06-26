---
layout: post
title:      "Selection Sort vs. Merge Sort"
date:       2019-06-26 15:39:50 +0000
permalink:  selection_sort_vs_merge_sort
---

Here I wanted to discuss the different sorting algorithms we can use to sort data to gain a better understanding of what goes on behind the scenes of an `Array.prototype.sort()` method in JavaScipt, and more.

## Selection Sort

Given an unsorted array, if we would like place numbers from the lowest to the highest, how would we start? We can find the minimum number, remove it from the array and push it into an empty array, then find the minimum of the remaining numbers, and so on until there are no numbers left in the original array.

So how do we write a function that will find the minimum number and remove it from the array? We have to go through the array one by one, holding onto the smallest number. We can set the minimum equal to the first element in the array, and replace it with the true minimum.

```
  function minAndRemove(array){
    let min = array[0]
    let minIndex = 0
    for(let i = 0; i < array.length; i++){
      let currentElement = array[i]
      if(array[i] < min){
        min = array[i]
        minIndex = i
      }
    }
    array.splice(minIndex, 1);
    return min;
  }
```

So we know we need to call this function until there are no elements left in the original array, and push them into an empty array one-by-one.

```
  function selectionSort(array){
    let newMin;
    let sorted = []
    while(array.length != 0){
      newMin = minAndRemove(array)
      sorted.push(newMin)
    }
    return sorted;
  }
```

What is the Big O of selection sort? First, we have to find the minimum and remove elemens until they are no more elements left in the original array. How many times do we have to call our `findMinAndRemove` function? One for each element, or `n` times the cost of our `findMinAndRemove` function.

So what's the cost of `findMinAndRemove`? we go through each element, and remove it. So the cost of `findMinAndRemove` is `n`, and we have to call it `n` times. so our big O is `n^2`.

## Merge Sort - Two sorted arrays to one
Here's an example of merge sort if we have two ordered arrays, and we need to combine them into one ordered array.

We know that if we have two sorted arrays, the array minimum of each array is going to be its first element, `array[0]`. So we first we need to figure out which of these two minimums is smaller.

```
let array1 =  [1, 2, 6, 7, 8]
let array2 =  [3, 4, 5, 9, 10]
 
function findMin(array1, array2){
  let minfirstArray = aarray1[0]
  let minsecondHalf = array2[0]
 
  if(minfirstHalf < minsecondHalf){
    return minfirstHalf
  } else {
    return minsecondHalf
  }
}
 
findMin(array1, array2)

// 1
```

Upon finding the minimum, we'll have to remove the element and push it into another array. So we will have to write a similar function to our `findMinAnd Remove` function from earlier, except it is for two sorted arrays.

```
let array1 =  [1, 2, 6, 7, 8]
let array2 =  [3, 4, 5, 9, 10]
 
function findMinAndRemove(array1, array2){
  let minfirstHalf = array1[0]
  let minsecondHalf = array2[0]
 
  if(minfirstHalf < minsecondHalf){
    return firstHalf.shift()
  } else {
    return secondHalf.shift()
  }
}
 
findMinAndRemove(array1, array2)
// 1
 
array1
// [2, 6, 7, 8]
```

So now, like with selection or insertion sort, we'll have to keep calling our FindMinAndRemove function until there are no elements left in either subarray.

```
  let array1=  [1, 2, 6, 7, 8]
  let array2 =  [3, 4, 5, 9, 10]
 
  function merge(firstHalf, secondHalf){
    let sorted = []
    let currentMin;
    while(array1.length != 0 && array2.length != 0){
      let currentMin = findMinAndRemove(array1, array2)
      sorted.push(currentMin)
    }
    return sorted.concat(array1).concat(array2)
  }
```

Our merge function for two arrays continues to  call findMinAndRemove until one of our arrays is empty. We know that once one of the arrays is empty, the other array only has teh remaining sorted elements, so we can cocatenate the remaining elements onto the end of our array. So in the above example:

```
  sorted
    // [1, 2, 3, 4, 5, 6, 7, 8]
array1
    // []
array2
    // [9, 10]
  sorted.concat(array1).concat(array2)
  // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```


Because we have to go through every element in each array, the big O of our merge algorithm is the combined length of our subarrays. O(a+b)

## Merge Sort - sorting one unsorted array 

To use merge sort to sort an unordered list into a sorted one using our merge sort algorithm above, we take the unordered list and continue splitting the list into smaller and smaller subarrays until each subarray contains just one element. The merge operation only applies to sorted lists, and lists with only one element are automatically sorted.

```
[7, 1, 2, 8, 6, 10, 4, 3]
 
[7, 1, 2, 8]   [6, 10, 4, 3]
 
[7, 1] [2, 8]   [6, 10] [4, 3]
 
[7] [1] [2] [8]  [6] [10] [4] [3]
```

Then, we couple up the neighboring lists and merge them together.

```
merge([7],[1]) 
merge([2], [8])  
merge([6], [10]) 
merge([4], [3])
```

Our merge operation takes two sorted arrays and combines them into one sorted array, so merging pairs of eight sorted arrays gives us four sorted arrays. Then merge again!

```
merge([7],[1]) merge([2], [8])  merge([6], [10]) merge([4], [3])
 
// [1, 7] [2, 8] [6, 10] [3, 4] 
 
merge([1, 7], [2, 8]) merge([6, 10] [3, 4])
 
// [1, 2, 7, 8]  [3, 4, 6, 10] 
// which we can merge again
merge([1, 2, 7, 8], [3, 4, 6, 10])
 
//[1, 2, 3, 4, 6, 7, 8, 10]
// providing us with a sorted array
```

That's it! We continuously divide an unsorted array until we have subarrays, each with one element. Then, we call the merge operation on the subarrays until we are left with one sorted array.

Here's an example of how we would code this solution. We can simplify the problem by starting with an unsorted array length of 2.

The first step is to split the array.

```
let unsortedArray = [2, 1]
 
function mergeSort(array){
  let midpoint = array.length/2
  let firstHalf = array.slice(0, midpoint)
  let secondHalf = array.slice(midpoint, array.length)
}
```

Now, note that `firstHalf` now equals `[2]` and `secondHalf` now equals `[1]`. Next, we merge this array together.

```
let unsortedArray = [2, 1]
 
function mergeSort(array){
  let midpoint = array.length/2
  let firstHalf = array.slice(0, midpoint)
  let secondHalf = array.slice(midpoint, array.length)
  return merge(firstHalf, secondHalf)
}
 
mergeSort(unsortedArray)
// [1, 2]
```

Next, we have to turn this into a recursive solution that will work for arrays of any length. With recursive solutions, we can think of a problem in terms of itself.

```
function mergeSort(array){
  let midpoint = array.length/2
  let firstHalf = array.slice(0, midpoint)
  let secondHalf = array.slice(midpoint, array.length)
  return merge(mergeSort(firstHalf), mergeSort(secondHalf))
}
 
mergeSort(unsortedArray)
```

Finally, we need to define the base case (or we'll get an endless loop). Our base case is when our array has only one element in it, we simply return the array. So our finished merge sort solution:

```
  function mergeSort(array){
    let midpoint = array.length/2
    let firstHalf = array.slice(0, midpoint)
    let secondHalf = array.slice(midpoint, array.length)
 
    if(array.length < 2){
      return array
    } else {
      merge(mergeSort(firstHalf), mergeSort(secondHalf))
    }
  }
```

So how costly is something like this when we think of Big O?  So notice for an array of length 8, we have to split it in half 3 times before the length is one? How many times do you have to split an array in half until the length is one? `log(n)`! 

Then remember earlier we said that the cost of our merge function was `array1.length` + `array2.length`? Or, just the length of the whole unsorted array, `n`.

So the cost of merging is `nlog n`. Much better than our `n^2` for selection sort.

![](https://media.giphy.com/media/ezjd4NlY4w3io/giphy.gif)

Thanks for reading!




















