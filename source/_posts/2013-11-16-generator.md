---
published: 
  - true
  - "true"
layout: post
title: How does generator behave

comments: "true"
categories: javascript
---

Generator is something new in ES6. It is quite interesting to explore how it behaves.

## Example

Here is a simple example

```javascript
/* jshint esnext:true */

var g = function* () {
  var a = yield 1;
  console.log(a);
  var b = yield 2;
  console.log(b);
  var c = yield 3;
  console.log(c);
};

var iterator = g();
var x = iterator.next('x');
console.log(x);
var y = iterator.next('y', 'dummy');
console.log(y);
var z = iterator.next('z');
console.log(z);
var _ = iterator.next('_');
console.log(_);
```

## Result

This script requires nodejs v0.11 with `--harmony-generators`

```bash
$ nvm use 0.11
Now using node v0.11.8
$ node --harmony-generators index.js
{ value: 1, done: false }
y
{ value: 2, done: false }
z
{ value: 3, done: false }
_
{ value: undefined, done: true }
```

Let's try to understand how this comes out.

## What happened

1. `function*` defines a generator. Calling the generator will create a new iterator.
2. `iterator.next()` will run the body of generator until next `yield` or end of generator and gets the yielded value as `{value: 'yielded value', done: false}`.
3. the generator is magically paused at `yield` until `iterator.next()` is called again, when it gets the return value from the **new** `iterator.next(value)` and continues execution.
4. if `iterator.next()` does not meet a yield but the function end, it gets the result `{value: undefined, done: true}`.

## Try to understand

The most magic part is in `yield`. The execution of `var a = yield 1` is splitted into two steps;

1. The first call `iterator.next('x')` will execute `yield 1` and gets the value `1` from it. But at this moment, `a` is not assigned any value yet. The argument of first `next()` call seems to be dummy. You could not catch `x` in generator as in the example above.
2. The second call `iterator.next('y')` will complete the execution of `yield 1`. The argument `'y'` becomes the return value of `yield 1`, thus `a` is assigned value `'y'`

Finally, `iterator.next('y')` will get value from next yield, i.e. `yield 2`.

## Summary

### excution sequence

iterator.next() => generator until yield or end => iterator.next() => generator until next yield or end

### value pass

1. `iterator.next()` gets the yielded value in `yield value`
2. `yield` gets value from **next** `next(value)`


## Reference

1. [node+harmony](http://code.shutterstock.com/presentations/node+harmony/
): nice presentation explaining the execution sequence well
2. [generators in v8](http://wingolog.org/archives/2013/05/08/generators-in-v8): example of using generator for async calls
