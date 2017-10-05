# Array.prototype.do proposal

This proposal introduces a new method to the Javascript Array prototype to support the common use case of performing side effects while iterating through an Array, while returning the original array paramenter. It is a pretty useful tool to help with the case where one finds themselves having to leave the chainability of function calls like `.map`, `.filter` and `.reduce`

## Inspiration

RxJS is an awesome library that almost lets you treat asynchronous events as elements in an array (called an Observable). RxJS has a [similarly named method on `Observable`](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-do) which is pretty useful.

## Introduction

The `Array.prototype.do` method is essentially synctactic sugar on a call to `Array.prototyp.map` that performs side effects before returning the first parameter of the callback.

For example:

```javascript
myArray.do(console.log)
```

is equivalent to 

```javascript
myArray.map(x => {
  console.log(x);
  return x;
})
```

## Proposed API 

```javascript
arr.do(function callback(currentValue, index, array) {
    // Return element for new_array
}[, thisArg])
```

### Parameters

`callback`
  Function that performs the intended side effect, taking three arguments:

  `currentValue`
    The current element being processed in the array.

  `index`
    The index of the current element being processed in the array.

  `array`
    The array `do` was called upon.
  
`thisArg`
  Optional. Value to use as `this` when executing callback.


### Return value

The original array that `.do` was called on

### Examples

Debugging (example inspired from [RxJS docs](http://reactivex.io/rxjs/class/es6/Observable.js~Observable.html#instance-method-do)

```javascript
let clicks = ... // events
let positions = clicks
  .do(console.log)  // easy to add line when debugging
  .map(ev => ev.clientX)
```

Filtering function applications

```javascript
let allFiles = ... // list of files
allFiles
  .do(someFunctionForAllFiles)
  .filter(s => s.endsWith('.js'))
  .do(someFunctionForJSFilesOnly)
```

