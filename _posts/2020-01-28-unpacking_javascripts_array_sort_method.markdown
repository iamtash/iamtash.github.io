---
layout: post
title:      "Unpacking JavaScript's Array Sort Method"
date:       2020-01-29 00:44:24 +0000
permalink:  unpacking_javascripts_array_sort_method
---


Let's begin by defining higher-order functions. Higher-order functions are functions that operate on other functions, either by taking them as arguments or by returning them ([Ref](https://eloquentjavascript.net/05_higher_order.html)). The JavaScript Array Sort method is one such function. `arr.sort()` takes an argument of a named or anonymous function (i.e., a callback) called a *compare function*. `arr.sort()` will pass two elements of the array at a time to the compare function, which will return a numerical value based on how the elements compare to one another. A negative return value means the first element is lesser than the second; a positive return value means the first is greater than the second, and a return value of zero means the two elements are equal. Once the sorting is complete, `arr.sort()` will return a new, sorted copy of the original array.

Let's look at how we would sort an array of numbers.
```
const numbers = [11, 5, 8, 1, 65, 80]

const compareFn = (a, b) => {
  if (a < b) return -1
  if (a > b) return 1
  return 0
}

numbers.sort(compareFn) // => [ 1, 5, 8, 11, 65, 80 ]
```
We can actually shorten this in two ways. First, we can simply pass in the compare function as an anonymous function when we call `numbers.sort()`:
```
numbers.sort((a, b) => {
  if (a < b) return -1
  if (a > b) return 1
  return 0
})
```
Second, because we are dealing with an array of *numbers*, we can simplify the callback even further:
```
numbers.sort((a, b) => a-b)
```
One-line arrow function syntax allows us to simply return `-1` or `1` without being verbose.

What if we have an array of strings? Not much different since JS inherently compares strings alphabetically, though we do need to be verbose in our compare function since an expression subtracting two strings will return `NaN`.
```
'apple' - 'peach' // => NaN

const fruits = ['peach', 'apple', 'grapefruit', 'coconut']

fruits.sort((a, b) => {
  if (a < b) return -1
  if (a > b) return 1
  return 0
})
// => [ 'apple', 'coconut', 'grapefruit', 'peach' ]
```
In this case we can actually just write `fruits.sort()` because by default, the `sort()` method sorts the values as strings in alphabetical order.

Okay cool, so what if we have an array of objects like this?
```
const objects = [
  {
    name: 'Natasha',
    age: 26
  },
  {
    name: 'DJ',
    age: 90
  }
  {
    name: 'Mark',
    age: 32
  }
]
```
We can simply work whichever property we want to sort by into our compare function, in this case `name`. Note that it's also important to normalize capitalization.
```
objects.sort((a, b) => {
    if (a.name.toLowerCase() < b.name.toLowerCase()
        ) return -1
    if (a.name.toLowerCase() > b.name.toLowerCase()
        ) return 1
    return 0
})
// => [ { name: 'DJ', age: 90 },
  { name: 'Mark', age: 32 },
  { name: 'Natasha', age: 26 } ]
```

Array sorting can and will most definitely get more complicated than this, but this is what I can confidently explain, for now. :)
<iframe src="https://giphy.com/embed/7DzlajZNY5D0I" width="480" height="310" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/girls-weird-sassy-7DzlajZNY5D0I">via GIPHY</a></p>

