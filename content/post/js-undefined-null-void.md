---
title: "JS: The difference between \"undefined\", \"null\" and \"void 0\""
draft: false
date: 2020-01-17T15:20:00Z
slug: "js-undefined-null-void"
tags: ["javascript", "ecmascript"]
---
### 🍎🍊🍌

Why am I even asking this question, you might think. Well, the thing is that I have been asked this recently and I feel that I didn't give a good enough answer.

Even though `undefined`, `null` and `void 0` have something in common, they cannot be compared directly because they represent different concepts with different functionalities.

Instead of doing a one to one comparison between those, I think it makes more sense to explain what each of them is and by doing this, it will be clear how different they are.

### `undefined`

It is a *global property* or a *primitive value*.

So, as you see, when you say "undefined" you could potentially be referring to two very different things.

The global property named `undefined` has a value of `undefined` by default. This property could be modified up until ES5, when it was made read-only. Therefore if you try to change its value, you won't be able to:

```js
undefined = 1

console.log(undefined) // undefined
```

There is a way though to override the value of the global `undefined` property, even in the latest version of EcmaScript. This can be done by creating a scoped variable called `undefined` and giving it an arbitrary value. We are basically shadowing the built-in `undefined`.

```js
(function() {
  var undefined = 1

  console.log(undefined) // 1
})()
```

When it comes to the value of `undefined`, this is the default value for any variable that has been declared but not initialised.

```js
var one

console.log(one) // undefined
```

Also, `undefined` is the value of an object's property that does not exist.

```js
var obj = {
  hello: 'world'
}

console.log(obj.goodbye) // undefined
```

### `null`

It is a *primitive value*.

Similarly to the `undefined` primitive value it is also falsy, but it is not an identifier or a global property.

Unlike `undefined`, it is not being assigned by default to anything in JavaScript. You can only manually set the value of `null`.

```js
var nothing = null

console.log(nothing) // null
```

The common use case for `null` is to assign it to an identifier where an object can be expected but none is relevant.

Because both `null` and `undefined` are falsy, when compared using the abstract comparison `==`, the result is going to be `true`. But, using the strict comparison `===`, the result is going to be `false`.

```js
console.log(null == undefined) // true
console.log(null === undefined) // false
```

### `void <expression>`

It is an *operator*.

Unlike both `undefined` and `null`, it does not represent a primitive value.

The connection between `void` and the other two is that it always returns the value of `undefined`.

Its purpose is to evaluate an expression (usually for its side effects) and then return `undefined`.

```js
console.log(void 0) // undefined
console.log(void (1 + 1)) // undefined
console.log(void (() => 5)) // undefined
```

One other use for `void` is to retrieve the original value of `undefined` when the `undefined` identifier could have been overridden.

```js
(function() {
  var undefined = 1

  console.log(undefined) // 1

  var realUndefined = void 1

  console.log(realUndefined) // undefined
})()
```

But, as you remember the global property `undefined` is read-only, so we can retrieve its value without using `void`, like so:

```js
(function() {
  var undefined = 1

  console.log(undefined) // 1

  console.log(global.undefined) // undefined
})()
```

### Conclusion

Quick recap:

`undefined` is a *global property* or a *primitive value*

`null` is a *primitive value*

`void <expression>` is an *operator*


As we have seen, we can find uses for all of them but only one of them is really indispensable: `undefined`.

We can easily get by without `null` and especially `void` which seems to be an artifact of the past.