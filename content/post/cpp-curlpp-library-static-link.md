---
title: "C++: Using 3rd party libraries in QT Creator"
draft: false
date: 2020-06-27T12:20:00Z
slug: "cpp-libraries-curlpp"
tags: ["c++", "curlpp", "dorinbrowser", "qt"]
---
### My first web request

While in the very early stages of building [Dorin Browser](https://github.com/dorin131/DorinBrowser), I got to the point when I had to implement a web request functionality that would let it fetch resources from the internet.

Being a total newbie when it comes to C++, I had no idea how to do this. I was still half way through my "The C++ Programming Language" book by Bjarne Stroustrup, and there was no mention yet on how to do this.

Being eager to get this pretty important feature added to the browser, I dockduckgo'd "how to make a web request in cpp" and the first [StackOverflow result](https://stackoverflow.com/questions/1011339/how-do-you-make-a-http-request-with-c) that popped up, had as answer the **curlpp** library.

This sounded good to me. It's a wrapper around the trusted **libcurl** library and the API seemed quite friendly.


### 3rd party libraries in C++

I felt like I was on the right path, now that I knew what library I'm going to use, but I had no idea how to actually get it into my project.

Most of the languages that I used to work with before, like JS, Java, Go, had some kind of a de facto package manager that would let you, relatively easy, add 3rd party packages to your project.

It seemed that even though there are some kind of package managers for C++, many developers prefer not to use one, and I was quite happy not to use one either. Having all my dependencies next to my source code, without needing to download stuff off the internet, was a very appealing idea and I still love it.

So, I had to figure this one out. My very brief research showed that there are two main ways of including libraries into a C++ project. That is, statiacally linking them or dynamically linking. 

**Static linking** will take the source code of your library and build it into your program at compile time.

**Dynamic linking** will compile the library in a separate file which your program is going to use it.

So I decided to go ahead with having **curlpp** as a statically linked library.


### My dev environment

I'm running on Linux Mint 19.1 and my IDE is QT Creator 4.12.3 based on Qt 5.14.2

If you're on a different platform or using a different IDE, you'll probably have to do things slightly different.


### Adding the library to the project


#### Dependencies

The first thing you want to do, is make sure you have **libcurl** installed on your machine.

```bash
sudo apt-get install libcurl4 libcurl4-openssl-dev -y
```

This is required because **curlpp** is basically a wrapper around **libcurl**.


#### Getting the source code

Next, we have to download the actual source code from https://github.com/jpbarrette/curlpp/releases/tag/v0.8.1 (pick the latest version) and copy it into your project folder. Depending on how you have structured your project, you might want to have it inside a /libs folder.

Now you should have the curlpp source code within **/your-project/libs/curlpp-x.x.x**


#### Compiling the library

Inside the curlpp folder that you have just copied (curlpp-x.x.x), we are going to create a build folder, where the library is going to be compiled.

```bash
mkdir build
cd build
cmake ..
make
```

Compiling the library is a necessary step for either statically or dinamically linking to it. That is unless you just download the binaries.

#### Adding the library to the project

Right click on your project and click on **Add Library...**

![Add library](/img/cpp-curlpp-library-static-link/1.png)

Select **External libarary**

![External library](/img/cpp-curlpp-library-static-link/2.png)

Choose the newly built library file **libcurlpp.a**. This is telling the IDE that you want to use a statically linked library.
Also select the **Include path**, which is a folder inside the **curlpp-x.x.x** folder.

![Choose library file](/img/cpp-curlpp-library-static-link/3.png)

QT Creator is going to auto-generate new config and place it in your project's *.pro file.
It is going to look something like this:

![Project file](/img/cpp-curlpp-library-static-link/4.png)


### Conclusion

And that was it. Nothing complicated once you know how to do it :)
Dorin Browser can now make web requests and get the beautiful source code which I yet have to parse and render.

![Project file](/img/cpp-curlpp-library-static-link/5.png)