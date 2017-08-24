+++
date = "2015-05-17T12:00:00"
draft = "false"
title = "How to make a class iterable in ES6"
slug = "how-to-make-a-class-iterable-in-es6"

+++

ECMAScript 6 (ES6) introduced the concept of [iterators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols). Iterators let you define custom ways to iterate or travers an object by either by calling `next()` or using the `for..of` construct.

In order to add iterability you have to implement the `Iterator` as well as the `Iterable` interface in your class.

	interface Iterator {
        next() : IteratorResult;
    }

    interface IteratorResult {
        value: any;
        done: boolean;
    }

    interface Iterable {
        [System.iterator]() : Iterator;
    }

The semantics of `next()` are quite easy:

* If you can produce a new value, return it and set `done` to false
* Once you iterated overall values, set `done` to `true` and return `value` as undefined

Iterators can be infinite and never return `done` as `true`.

Once the Iterator interface is implemented, implementing Iterable becomes trivial as you just return `this`.

Here is a complete example of the concept.

<script src="https://gist.github.com/olsio/456c0e6f761d0462004a.js"></script>

Output:

    Red
    Blue
    Yellow
    Black