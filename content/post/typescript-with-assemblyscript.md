---
title: "Supercharge your TypeScript with AssemblyScript"
draft: false
date: 2019-12-01T10:30:00Z
slug: "typescript-with-assemblyscript"
tags: ["webassembly", "typescript", "assemblyscript"]
---

### Introduction
So you’re using TypeScript at work or your personal project? That’s fantastic! Take advantage of those types and produce better, more readable and bug-free JavaScript!
And now you’re looking to take things to the next level and get super-fast execution times? You’re in the right place then.

### WebAssembly
You’ve probably heard of WebAssembly already. If not, here is a brief description of what WebAssembly is:

> WebAssembly (abbreviated Wasm) is a binary instruction format for a stack-based virtual machine. Wasm is designed as a portable target for compilation of high-level languages like C/C++/Rust, enabling deployment on the web for client and server applications. (https://webassembly.org/)

Yes, you read that right, you can write code in your favourite mega-efficient low level programming language, compile that to WebAssembly (binary V8 code) and call it from within your JS/TS code!

### AssemblyScript
What if I told you than you don’t need to know/learn a new language to write dazzling-quick TypeScript? With the help of AssemblyScript, you can write TypeScript-like code with real types and better performance, which compiles to WebAssembly.

So what is AssemblyScript?

> AssemblyScript compiles a strict subset of TypeScript (a typed superset of JavaScript) to WebAssembly using Binaryen. (https://docs.assemblyscript.org/)

It’s as simple as that. You write code in a language which is basically a subset of TypeScript which is going to compile to WebAssembly.

### How do I get this to work?
I’m glad you asked!

If you start a project from scratch you can follow the steps below exactly. Otherwise if you’re adding AssemblyScript to an existing project, you might want to skip some steps or do them slightly differently, depending on what you’ve already got.
You can also clone the the demo repository I have created just for you: 
> https://github.com/dorin131/assemblyscript-in-typescript

1. `npm init`
  
    Initialising NPM.

2. `npm i -S typescript assemblyscript @types/node`
   
    Installing TypeScript, AssemblyScript and Node types.

3. `npx tsc --init`
   
    Initialising TypeScript. This will create a tsconfig.json file in your root directory.

4. `npx asinit .`
   
    Initialising AssemblyScript. This will add a few NPM scripts in your package.json and also add a few other files which will help us to get started with AssemblyScript much quicker.
    In tsconfig.json add the properties below. We are basically just configuring the TypeScript compiler a bit by setting the root and destination paths and telling it to ignore the `/node_modules` and `/assembly` folders.
      ```json
      {
        "compilerOptions": {
          "outDir": "./dist",
          "rootDir": "./"
        },
        "exclude": [
          "node_modules",
          "assembly"
        ]
      }
      ```

5. In `package.json` add the scripts below. Every time you make a change to your code, you will have to run npm run build and then `npm start`. Notice how when we start with Node, we pass two flags to it: `--experimental-wasm-modules` and `--experimental-modules`. This is required, so that Node can load the *.wasm files. Also make sure you’re using at least version 12 of Node.
      ```json
      {
        "start": "node --experimental-modules --experimental-wasm-modules ./dist/index.js",
        "build": "npm run asbuild && tsc -p ./tsconfig.json && npm run copy",
        "copy": "rm -rf dist/build/ && cp -r build dist/build/"
      }
      ```

6. Rename `index.js` to `loader.ts`, add any missing types and export the WebAssembly instance to be used from other files (https://github.com/dorin131/assemblyscript-in-typescript/blob/master/loader.ts)

### Demo benchmark
By implementing the fibonacci sequence function, which takes number of the fibonacci element and returns its value, in both TypeScript and AssemblyScript, I wanted to run a very simple benchmark.
This is the TypeScript code:

```ts

function fibonacci(n: number): number {
  return n < 1 ? 0
       : n <= 2 ? 1
       : fibonacci(n - 1) + fibonacci(n - 2);
}
```

And the AssemblyScript code:

```ts
export function fibonacci(n: i32): i32 {
  return n < 1 ? 0
       : n <= 2 ? 1
       : fibonacci(n - 1) + fibonacci(n - 2);
}
```

Running both of these with an input of **45**, resulted in the same output **1,134,903,170**, but different times!

The TypeScript version took **9331.856ms**

The AssemblyScript version took **7285.571ms**

A performance improvement of **22%** in this specific case.

### Conclusion
As you can see, getting started with AssemblyScript is very easy and the syntax is almost the same as TypeScript’s (remember, it’s a subset).

In our very simple fibonacci example we saw a performance increase of **22%**, but I am quite sure that the impact can be much greater in more complex code, where the V8 engine will struggle to optimise the untyped JavaScript.