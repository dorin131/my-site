---
title: "WebAssembly is easy — a hello world example"
draft: false
date: 2019-12-07T21:05:00Z
slug: "webassembly-hello-world"
tags: ["webassembly", "hello world"]
---

### Introduction
I like to think of [WebAssembly](https://webassembly.org/) as of [Assembly](https://en.wikipedia.org/wiki/Assembly_language). It gives you a few simple building blocks that you can arrange and combine to create almost anything. It’s a bit like playing with Legos.

As a new technology though, it has a few entry barriers which can put off someone who just wanted to try it out. The code that is usually referred to as the “glue” between WASM and JS is not pretty and requires you to have a more in-depth knowledge of WASM to be able to make sense of or put together.

But there are ways to make developing in WebAssembly easy and enjoyable. I am going to talk about them below.


### Your first “Hello World” in WASM
It has become a tradition already to first attempt to write a “Hello World” application in a new language that you are trying out. Usually this will just print out these words to the standard output or in some other visual way.

In WASM, it is a bit different though. The “Hello World” equivalent is often an addition function, which takes two integer arguments and returns their sum. There is a good reason why we are not attempting to print a string. Strings do not exist in WebAssembly as a type. We can have a string of bytes in memory which have a string encoded but any manipulation will have to be done on a byte level.
This is our addition function in WASM (text format):

```js

(module
  (func (export "add") (param $n1 i32) (param $n2 i32) (result i32)
    get_local $n1
    get_local $n2
    i32.add
  )
)
```

Let’s break this down quickly and see what’s happening there line by line:

1. We declare a new module. All of your WebAssembly code has to be contained in a module.
2. Declaring a function which we export with the name `add`. This will allow us to call it from JS with `add()`. Then we say that it has two parameters of type 32bit Integer named `$n1` and `$n2`. Lastly we say that our punction is going to return another 32bit Integer.
4. Put on stack `$n1` from local memory.
5. Put on stack `$n2` from local memory.
6. The built-in `i32.add` function will take the last two values from the stack, add them and return the sum.

That it pretty much it. The syntax is not C/JS-like but pretty easy to understand. Every element is a node and we can have nodes nested in other nodes, which act as parameters.


### How to run it
Now that you have your very first WebAssembly application, you want a quick and easy way to test it. This is where people often stumble.

To be able to test this function you will have to load the WASM code into JavaScript and call it from there. Our goal is to be able to call our `add()` function with two arguments and read the output number.

The easiest way to do this, that I know if, is using [inline-webassembly](https://www.npmjs.com/package/inline-webassembly) NPM package. And you would end up with a JS file like this:

```js
const iw = require('inline-webassembly');

iw(`
  (module
    (func (export "add") (param $n1 i32) (param $n2 i32) (result i32)
      get_local $n1
      get_local $n2
      i32.add
    )
  )`
).then((wasmModule) => {
  const sum = wasmModule.add(44, 99);
  console.log(`Sum = ${sum}`); // 143
});
```


### Conclusion
Now that you know how you can easily create and use WebAssembly code, there is nothing stopping you from refactoring performance critical parts of your code using WASM.
 
Happy assembling!