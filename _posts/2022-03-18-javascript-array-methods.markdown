---
layout: post
title:  "Javascript Array Methods"
date:   2022-03-18 09:30:00 -0500
categories: [Programming, Javascript]
---

# Introduction
In this post we’re going to be going over some of the handy, built in array methods in javascript. I won’t be going over every single method that exists on the array object in javascript but, I will go over the ones I use a lot and some of the methods that might come in handy for you if you are a javascript developer. This post is intended more for beginner javascript developers so forgive me if these are all methods you are already familiar with. If you are looking for a very in depth description of all of the available methods on the array object, Mozilla has some really amazing documentation that can be found [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array).

# Array.at(index)
The first array method we’ll be going over is the `at(index)` method. Note that the `at(index)` method is not fully supported in all browsers at the time of writing this post. Internet explorer has no support for it at all and at the time of writing safari support is experimental. The `at(index)` method takes in an integer (positive or negative) and returns the value for that index in the array. If you give it a negative number it will start at the end of the array and count backwards. If you pass an integer that points to an index that does not exist, the method will return undefined. Below are a few examples of the `at(index)` method in action.

```
let myArray = ['a','b','c','d'];

console.log(myArray.at(0));
// Outputs: 'a'

console.log(myArray.at(2));
// Outputs: 'c'

console.log(myArray.at(-3));
// Outputs: 'b'

console.log(myArray.at(10));
// Outputs: 'undefined'
```

# Array.concat(anotherArray, ...)
The `concat(anotherArray, …)` method creates a new array made up of the array you are calling concat on and the array(s) you pass in as arguments to concat. Concat doesn’t modify any of the arrays passed as arguments or the array it is called on, it instead returns a new array. Note that you can also do things like concat an array to itself to get a new array with two of each element from your array inside it. Below are a few examples of what this would look like:

```
let myArray = ['a', 'b', 'c'];
let myArray2 = [1 ,2, 3];
let myArray3 = [0, 5, 9];

let newArray = myArray.concat(myArray2);
// newArray is now ['a', 'b', 'c', 1, 2, 3]

newArray = myArray.concat(myArray2, myArray3);
// newArray is now ['a', 'b', 'c', 1, 2, 3, 0, 5, 9]

newArray = myArray.concat(myArray);
// newArray is now ['a', 'b', 'c', 'a', 'b', 'c']

newArray = myArray.concat('def');
// newArray is now ['a', 'b', 'c', 'def']

newArray = myArray.concat(['d', 'e', 'f']);
// newArray is now ['a', 'b', 'c', 'd', 'e', 'f']

newArray = myArray.concat(['d', 'e', 'f'], 'g');
// newArray is now ['a', 'b', 'c', 'd', 'e', 'f', 'g']
```

# Array.filter(function)
The time is here, we have run into our first method that takes in a function as an argument. These are always the ones I find confuse people who are new to array methods the most. I’ll do my best to explain this in a way that will make sense. The `filter` method allows you to pass all data in an array through a function and get back a new array for everything that returned true in that function. This probably doesn’t make much sense yet so let’s look at an example. Below is an array filter that will filter out any value less than 10 from our array.

```
let numbers = [0 ,2, 3, 5, 9, 12, 15, 23];

let numsGreaterThanTen = numbers.filter(number => { 
    return number > 10;
});

console.log(numsGreaterThanTen);

// Outputs: [12, 15, 23]
```

Let’s break down what’s happening here. First we are starting off with an array of numbers. Next we are calling the `filter` method on our array and passing it an anonymous function. This post will assume you are familiar with anonymous functions, if not feel free to reach out and I can recommend some good resources. Our function has one argument called `number`. This argument can have any name, it represents each item in your array. Our array is full of random numbers so I went with the name number on this one. If you had an array of say ducks, you would probably name that argument duck. The filter will iterate over your array, passing each number through the function we passed in. The array that our filter hands back will contain every item from the original array where the function evaluated true when passed that value. Below is the exact same functionality that the filter provides but in a way that may seem more familiar to you just in case that helps it sink in.  The array filter above would provide the same results as something like this:

```
let numbers = [0 ,2, 3, 5, 9, 12, 15, 23];
let numsGreaterThanTen = [];

for (let i = 0; i < numbers.length; i++) {
    if (numbers[i] > 10) {
        numsGreaterThanTen.push(numbers[i]);
    }
}

console.log(numsGreaterThanTen);
// Outputs: [12, 15, 23]
```

Boilerplate code similar to that above is where the motivation for a lot of these array methods came from. Back in the day we would have to use for loops to filter data out of arrays or transform data inside of arrays. This could be a big pain and lead to a lot of boilerplate on codebases that do things like manipulating the structures of a bunch of objects in an array. Using things like `filter` and `map` can cut down on a lot of repeated code and make things a lot easier to read.

# Array.find(function)
Array `find` in javascript works almost exactly the same as the `filter` method with one key difference. It will only return one object instead of an array of objects. It runs all of the items in your array through the function you provide as an argument. The first item in the array to return true from the function you pass in will be the result. If nothing matches it will return undefined. Below is an example of an array `find`:

```
let numbers = [0 ,2, 3, 5, 9, 12, 15, 23];

let numsGreaterThanTen = numbers.find(number => { 
    return number > 10;
});

console.log(numsGreaterThanTen);

// Outputs: 12 as that is the first element in our array that returns true in our number > 10 check

let numsGreaterThanTen = numbers.find(number => { 
    return number > 100;
});

console.log(numsGreaterThanTen);

// Outputs: undefined because none of the items in 'numbers' are > 100
```




# Array.findIndex(function)
Array `findIndex` in javascript works almost exactly the same as the `find` method but, it returns the index of the first array element that passes our check rather than returning the element itself. With `findIndex`, if nothing passes our check instead of undefined we get back -1. Below is our `find` example modified to use `findIndex`:

```
let numbers = [0 ,2, 3, 5, 9, 12, 15, 23];

let numsGreaterThanTen = numbers.findIndex(number => { 
    return number > 10;
});

console.log(numsGreaterThanTen);

// Outputs: 5 as that is the index of the first element in our array that returns true in our number > 10 check

let numsGreaterThanTen = numbers.find(number => { 
    return number > 100;
});

console.log(numsGreaterThanTen);

// Outputs: -1 because none of the items in 'numbers' are > 100
```

# Array.isArray(variable)
Unlike the other array methods we don't call this method on the array itself. It is a static method on the Array class that we call directly and pass a variable of some sort. This method will return true or false based on if the variable passed in is an array or not. Below are some example usages:

```
// Checking valid arrays
Array.isArray([]) // Outputs true
Array.isArray([1, 6, 5]) // Outputs true

// Checking non array arguments
Array.isArray({}) // Outputs false
Array.isArray(1) // Outputs false
Array.isArray(null) // Outputs false
Array.isArray(false) // Outputs false
```

# Array.map(function)
Array `map` is very similar to filter. It takes in a function and loops through our array running each element through our function. The big difference is that instead of filtering data out of an array map is for transforming data. For instance, if I had an array of numbers and I wanted to multiply each one by 5, I could do something like this:

```
let numbers = [1,2,3];

let numbersTimesFive = numbers.map(number => {
    return number * 5;
});

console.log(numbersTimesFive);
// Outputs: [5, 10, 15]
```

A more common use case in the real world would be something like working with an API integration. You may be doing something like hitting a third party API to grab an array of objects. You may want to rename the keys in that object to match your database schema or something like that, a map comes in super handy for things like this. Below is an example of how this would work with an array of objects:

```
let games = [
    {
        longTitleKey: "My game 1",
        longRatingKey: "E"
    },
    {
        longTitleKey: "My game 2",
        longRatingKey: "M"
    }
];

let changedMaps = games.map(game => {
    return {
        title: game.longTitleKey,
        rating: game.longRatingKey
    }
});

console.log(changedMaps);
/*
Outputs: 
[
    {
        title: "My game 1",
        rating: "E"
    },
    {
        title: "My game 2",
        rating: "M"
    }
]
*/
```

# Wrapping Up
I hope this post was able to add some fun new tricks to your book of working with arrays in javascript. As always thank you for reading and if you have any questions don't hesitate to reach out!