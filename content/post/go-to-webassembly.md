---
title: "Compile Go to WebAssembly (the browser version)"
draft: false
tags: ["go", "webassembly", "compile", "browser"]
date: 2019-12-23T17:40:00Z
slug: "go-to-webassembly"
---

### Introduction

I don't think many people realised yet the great possibilities we were given with the advent of WebAssembly.

We are now essentially able to compile almost any ([see list](https://github.com/appcypher/awesome-wasm-langs)) language to WebAssembly. Which means that the range of applications that we can run in a browser, on the client side, has grown exponentially!

For example, I would like to show you below how you can take your Go code, compile it to WebAssembly and run it natively in the browser.

### 🍖 & 🥔

Let's to this step by step!

1. Prepare a Go application that you want to convert to WebAssembly. NOte that you can only compile `main` packages. If your code is not part of a main package then make it so.

Here is what I am going to compile:

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello world!")
}

```

Classic.

2. Run the go command to compile your Go code to WASM.

```sh
GOOS=js GOARCH=wasm go build -o main.wasm
```
3. Copy the glue JS file which will let you execute the compiled WASM in the browser. This is a file located inside your `$GOROOT` directory.

```sh
cp "$(go env GOROOT)/misc/wasm/wasm_exec.js" .
```
4. Create a HTML file with the following contents. Note that we are including the `wasm.exec.js` file that we just copied.
```html
<html>
	<head>
		<meta charset="utf-8"/>
		<script src="wasm_exec.js"></script>
		<script>
			const go = new Go();
			WebAssembly.instantiateStreaming(fetch("main.wasm"), go.importObject).then((result) => {
				go.run(result.instance);
			});
		</script>
	</head>
	<body></body>
</html>
```
5. Now you have to spin up a local web server and load the HTML file through it. If you're not sure how to do this, there is a quick way to do it with Go. Just execute the following two commands in the terminal.

```sh
go get -u github.com/shurcooL/goexec
goexec 'http.ListenAndServe(`:8080`, http.FileServer(http.Dir(`.`)))'
```

If everything went well you'll be able to navigate to `localhost:8080` and be presented with a blank page. Now open the developer tools and check the console!

Great success!

### Automating the process
I have also put together a little script which lets you compile and start the server in one command.

Check out the repository below.

[github.com/dorin131/go-to-webassembly](https://github.com/dorin131/go-to-webassembly)

By running `compile.sh`, all the steps above are going to be executed.