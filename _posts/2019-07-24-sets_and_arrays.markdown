---
layout: post
title:      "Sets and Arrays"
date:       2019-07-24 18:43:50 +0000
permalink:  sets_and_arrays
---


In general, an Array is a type of data structure representing a block of data allocated in consecutive memory by index. For example, `[8, 7, 9, 8, 9]`. A set is an abstract data type which contains only distinct elements or objects wihout the need of being allocated in order by index. For example, `{8, 7, 9}`.

Some key differences between an Array and a Set: 
* In a Set, elements are unique and cannot be duplicated. 
* An Array is an "indexed collection" and a Set is a "keyed collection"
   * Indexed collections are collections of data that are ordered by an index value
   * Keyed collections use keys and are iterable in the order of insertion

To declare a new Array in JavaScript, you can either declare a literal `let arr = [8, 7, 9, 8, 9];` or use a built-in constructor function `let arr = new Array(8, 7, 9, 8, 9);`. 

To declare a new Set, we can only use the built-in constructor function. There is no literal short cut. `let set = new Set([8, 7, 9]);`. Set receives an iteratable object as its input parameter and will create a set object respectively. So we can construct a set from an array, but it will only include unique and distinct elements- no duplicates.

We can also convert a set to an array using the `Array.from()` method.
```
var set = new Set([1,2,3]); // {1,2,3}
var arr = Array.from(set);//[1,2,3]
```

With Arrays, we can access elements by index using bracket notation, but Set does not support this. Since Array data is stored in consecutive memory, access time will be quicker and more efficient compared to abstract data types such as Sets.

The syntax for checking if an element is in a set, compared to an array:
```
set.has(0);  // false (boolean)
arr.includes(0); //false (boolean)
arr.indexOf(0);  // -1 (returns the index if the element is in the array, -1 if not)
```

Adding or inserting a new element into an array has linear run time, O(n) in the worst-case scenario where an element is added to the beginning of an array and each element must be shifted by one index. In Set, because all of the elements must be unique, each element will need to be checked to make sure there is no duplicate before adding a new element. So for Adding/Inserting, Sets and Arrays perform similarly in regard to runtime.

To remove an element, Arrays have many different built-in methods, while Sets only have `delete()` and `clear()`.



