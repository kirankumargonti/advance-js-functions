# Advanced JavaScript Functions to Improve Code Quality

This repository contains examples and explanations of advanced JavaScript functions designed to enhance code quality, performance, and maintainability. These functions cover various techniques such as debouncing, throttling, memoization, and functional programming concepts like currying and composition.

## Table of Contents

- [Introduction](#introduction)
- [Debounce](#debounce)
- [Throttle](#throttle)
- [Once](#once)
- [Memoize](#memoize)
- [Curry](#curry)
- [Partial](#partial)
- [Pipe](#pipe)
- [Compose](#compose)
- [Pick](#pick)
- [Omit](#omit)
- [Zip](#zip)

## Introduction

JavaScript offers a rich set of features and libraries that allow developers to write efficient, scalable, and maintainable code. This document explores advanced JavaScript functions that can significantly improve the quality of your code by reducing complexity, enhancing performance, and promoting better coding practices.

## Debounce

Debouncing is a technique used to ensure that a function does not execute until after a certain amount of time has passed since the last time it was invoked. This is particularly useful for events that trigger frequently, such as window resizing or scrolling.

```javascript
function debounce(func, delay) {
  let timer
  return (...args) => {
    clearTimeout(timer)
    timer = setTimeout(() => {
      func.apply(this, args)
    }, delay)
  }
}
```

**Usage Example**

```javascript

function someFunction(arg) {
  console.log(arg)
}

const debouncedFunction = debounce(someFunction, 1000)

// This will only call someFunction once after 1000ms of inactivity

debouncedFunction('Hello') // Call 1
debouncedFunction('World') // Call 2 (ignored)
debouncedFunction('!') // Call 3 (ignored)

// After 1000ms of inactivity, this will call someFunction("!")
```

## Throttle

Throttling limits the rate at which a function can fire. This is useful for events that should not fire too often, such as resizing windows or scrolling pages.

```javascript
function throttle(func, limit) {
  let inThrottle = false

  return function (...args) {
    const context = this
    if (!inThrottle) {
      func.apply(context, args)
      inThrottle = true
      setTimeout(() => (inThrottle = false), limit)
    }
  }
}
```

**Usage Example**

```javascript
// Define the function that updates the layout
function updateLayout() {
  console.log('Resize event handler called')
}

// Create a throttled version of the function
const handleScroll = throttle(updateLayout, 2000)

// Listen for window scroll events and call the throttled function
window.addEventListener('resize', handleScroll)
```
## Once

The `once` function ensures that a given function is executed only once, regardless of how many times it is called.

```javascript
function once(func) {
    let called = false;
    let result;
    return function(...args) {
        if (!called) {
            called = true;
            result = func.apply(this, args);
        }
        return result;
    };
}
```

**Usage Example**

```javascript
const firstTask = once(function() {
    console.log('First task executed!');
    return 'First task result';
});

const secondTask = once(function() {
    console.log('Second task executed!');
    return 'Second task result';
});

console.log(firstTask()); // Logs 'First task executed!' and returns 'First task result'
console.log(firstTask()); // Just returns 'First task result'
console.log(secondTask()); // Logs 'Second task executed!' and returns 'Second task result'
console.log(secondTask()); // Just returns 'Second task result'

```

## Memoize

Memoization is a technique used to speed up programs by storing the results of expensive function calls and reusing them when the same inputs occur again.

```javascript
function memoize(func) {
    const cache = new Map();
    return function(...args) {
        const key = JSON.stringify(args);
        if (cache.has(key)) {
            return cache.get(key);
        }
        const result = func.apply(this, args);
        cache.set(key, result);
        return result;
    };
}

```

**Usage Example**

```javascript
const expensiveCalculation = memoize(function(x, y) {
    console.log('Calculating...');
    return x + y;
});

// Testing the memoize function
console.log(expensiveCalculation(2, 3)); // Logs 'Calculating...' and returns 5
console.log(expensiveCalculation(2, 3)); // Returns 5 from cache, without logging 'Calculating...'
console.log(expensiveCalculation(4, 5)); // Logs 'Calculating...' and returns 9
console.log(expensiveCalculation(4, 5)); // Returns 9 from cache, without logging 'Calculating...'

```

## Curry

Currying is a technique in functional programming where a function with multiple arguments is transformed into a sequence of functions, each with a single argument.

```javascript
function curry(func) {
    return function curried(...args) {
        if (args.length >= func.length) {
            return func.apply(this, args);
        } else {
            return function(...moreArgs) {
                return curried.apply(this, args.concat(moreArgs));
            };
        }
    };
}

```

**Usage Example**

```javascript
function add(a, b, c) {
    return a + b + c;
}

const curriedAdd = curry(add);

console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
console.log(curriedAdd(1)(2, 3)); // 6
console.log(curriedAdd(1, 2, 3)); // 6

```

## Partial

Partials are functions that take a function and some arguments, and return a new function that, when called, passes the remaining arguments to the original function.

```javascript
function partial(func, ...fixedArgs) {
    return function(...remainingArgs) {
        return func.apply(this, [...fixedArgs, ...remainingArgs]);
    };
}

```

**Usage Example**

```javascript
function greet(greeting, name) {
    return `${greeting}, ${name}!`;
}

const sayHelloTo = partial(greet, 'Hello');

console.log(sayHelloTo('Alice')); // 'Hello, Alice!'
console.log(sayHelloTo('Bob'));   // 'Hello, Bob!'


function add(a, b, c) {
    return a + b + c;
}

const add5And3 = partial(add, 5, 3);

console.log(add5And3(2)); // 10
console.log(add5And3(4)); // 12


```

## Pipe

Piping is a concept borrowed from Unix/Linux pipelines, where the output of one command is piped as input to another. In JavaScript, piping allows you to chain functions together, passing the result of one function as input to the next.

```javascript
function pipe(...functions) {
    return function(initialValue) {
        return functions.reduce((acc, fn) => fn(acc), initialValue);
    };
}

```

**Usage Example**

```javascript
const double = x => x * 2;
const increment = x => x + 1;
const square = x => x * x;

const processNumber = pipe(double, increment, square);

console.log(processNumber(3)); // 49

```

## Compose

Composition is the process of combining simple functions to create more complex ones. In JavaScript, composing functions allows for cleaner, more readable code.

```javascript
function compose(...functions) {
    return function(initialValue) {
        return functions.reduceRight((acc, fn) => fn(acc), initialValue);
    };
}

```

**Usage Example**

```javascript
const double = x => x * 2;
const increment = x => x + 1;
const square = x => x * x;

const processNumber = compose(square, increment, double);

console.log(processNumber(3)); // 49

```

## Pick

The `pick` function creates a new object with only the specified properties from the original object.

```javascript
function pick(obj, keys) {
    return keys.reduce((acc, key) => {
        if (obj && Object.prototype.hasOwnProperty.call(obj, key)) {
            acc[key] = obj[key];
        }
        return acc;
    }, {});
}

```

**Usage Example**

```javascript
const user = {
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    age: 30
};

const pickedUser = pick(user, 'name', 'email');

console.log(pickedUser);
// Output: { name: 'John Doe', email: 'john@example.com' }

```

## Omit

The `omit` function creates a new object with all properties except those specified.

```javascript
function omit(obj, ...keys) {
    return Object.keys(obj).reduce((acc, key) => {
        if (!keys.includes(key)) {
            acc[key] = obj[key];
        }
        return acc;
    }, {});
}

```

## Zip

Zipping combines corresponding elements from multiple arrays into tuples.

```javascript
function zip(...arrays) {
    const length = Math.min(...arrays.map(arr => arr.length));
    return Array.from({ length }, (_, index) => arrays.map(arr => arr[index]));
}

```

**Usage Example**

```javascript
const names = ['Alice', 'Bob', 'Charlie'];
const ages = [30, 25, 35];
const cities = ['New York', 'San Francisco', 'Seattle'];

const zipped = zip(names, ages, cities);

console.log(zipped);
// Output: [ ['Alice', 30, 'New York'], ['Bob', 25, 'San Francisco'], ['Charlie', 35, 'Seattle'] ]

```

## Further Reading

- [MDN Web Docs - JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [Functional Programming in JavaScript](https://www.freecodecamp.org/news/javascript-functional-programming-concepts-examples/)
- [Mastering JavaScript Function Patterns](https://www.sitepoint.com/mastering-javascript-function-patterns/)

This repository aims to serve as a practical guide and resource for developers looking to leverage advanced JavaScript functions to write better, more efficient code.
