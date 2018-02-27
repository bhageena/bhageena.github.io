---
layout: post
title: WebAssembly - Then, Now and Ahead
description: "webassembly - then, now and ahead"
categories: blog
comments: true
tags: "WebAssembly EmergingTech"
excerpt: "[WebAssembly](http://webassembly.org/) is an emerging standard to enable near-native performance for web applications. As the name itself suggests, it's an assembly language for the web, a low-level compile target. It's an attempt to bring new low level primitives to the web."
share: true
readtime: true
---
### What is WebAssembly

[WebAssembly](http://webassembly.org/) is an emerging standard to enable near-native performance for web applications. As the name itself suggests, it's an assembly language for the web, a low-level compile target. It's an attempt to bring new low level primitives to the web.

### How it started *Then*

 > “Who we are cannot be separated from where we're from.”
― Malcolm Gladwell, Outliers: The Story of Success

WebAssembly was first [announced](https://brendaneich.com/2015/06/from-asm-js-to-webassembly/) on *17 June 2015* and since then it's been getting ever increasing attention, with major browsers -- Chrome, Edge, Firefox, and Safari, reaching consensus on the design of the initial [MVP](http://webassembly.org/docs/mvp/), in *March 2017*.

But it's important to know what was simmering even before that. In fact, its was ever insatiable quest for performance - JavaScript engines had become fast enough to run large compiled codebases, In the late 2008 and early 2009, V8, TraceMonkey, and Nitro were released, and the race for JavaScript speed was heating with browsers introducing just-in-time compilers.

JavaScript is standard(ECMAScript) based and the only language that runs in all web browsers. You can run only JavaScript in the browsers! all right, but you can write code in another languages - if you [compile](https://github.com/jashkenas/coffeescript/wiki/List-of-languages-that-compile-to-JS) it to JavaScript and compiled JavaScript can be faster than *regular* handwritten JavaScript. So you will have best of both worlds -- performance, safety, code/skills re-usability of existing applications, written in non JavaScript language and they can also run inside browsers or node powered servers. Little wonders all are talking about WebAssembly!

But, how compiled JavaScript can be faster, as it's just a subset of JavaScript! Well, let's take one step back and see how compilation works:

>   C/C++    =>    LLVM    =>    Emscripten    =>    JavaScript

The [LLVM](http://www.aosabook.org/en/llvm.html) Project is a collection of modular and reusable compiler and toolchain technologies, it's optimizer uses type information to perform many useful optimizations. Decades of work has gone into developing optimization passes for C/C++ compilers. These optimization are only available for compiled code, running them manually on a *normal* JavaScript codebase would be hard and make the code less maintainable.

Modern JavaScript engines infer types at runtime This especially helps on code that is implicitly typed - which is exactly what compiled code is!

ASM.JS, a high performance limited subset of JavaScript, can be considered precursor of WebAssembly. It's a research project at Mozilla that aims to formally define the subset of JavaScript that compilers like `Emscripten` and `Mandreel` already generate (typed arrays as memory, etc.).  asm.js code avoids potential slowdowns in code - no variables with mixed types, etc. It does only low-level assembly-like computation, precisely what compiled C/C++ needs. Variable types pop out during type checking. This makes it possible to do ahead of time (AOT) compilation, not only just in time (JIT). JavaScript engine has a guarantee that there are no speed bumps - variable types won't change, etc. - so it can generate simpler and more efficient code. The asm.js type system makes it easy to reason about global program structure - function calls, memory access, etc.

JavaScript engines have been a major focus of browser developers for some years, and the result has been substantial performance improvements from every vendor. JIT compilation that turns JavaScript code into instructions that can be directly executed on the processor brought huge speed gains. New data types have been added to the language to reduce the overhead when crunching numbers, and combined with `asm.js`, applications running in the browser can achieve performance that's comparable with that of native code.

In the quest for ever faster JavaScript, there has been a recurring refrain -- why use *JavaScript at all?* In spite of these improvements, the question of *why JavaScript?* remains. This is not without reason. The use of JavaScript incurs certain overheads -- browsers have to read and interpret a text-based language that was designed for human authors, not for machines. The design of JavaScript itself has features that are suboptimal from a performance perspective; the way a single JavaScript variable may at different times represent a number, a string, or a fragment of HTML means that a JIT compiler may not be able to optimize as aggressively as it would like. The ability to modify the behavior of even built-in objects such as arrays can be similarly problematic.

JavaScript does have certain important advantages: it's a memory safe, sandboxed environment, except for the browser bugs JavaScript programs can't escape beyond the confines of the browser to access sensitive data or install persistent malware. JavaScript is also processor independent, so scripts will run just as well on an x86 PC as they will on an ARM smartphone.

However, there are well-known ways of providing the advantages of JavaScript without those perceived downsides: bytecode runtimes like Java and .NET. Unlike script files, the bytecode represents a low-level, fairly compact representation of a program. Bytecodes also tend to be much easier for computers to read and JIT compile. Bytecode systems tend to map cleanly to the underlying arithmetic capabilities of the processor, too; they tend to operate on simple integers and floating point numbers, thereby avoiding the complexity of JavaScript's object system.

As such, there has long been a degree of pressure to use a bytecode system for the browser. Both Microsoft and Sun (now Oracle) did this with .NET and Java, respectively, but these systems were dependent on plugins, rather than being integrated into the browser's rendering engine the way JavaScript is. JavaScript programs could directly manipulate HTML objects; the plugins were instead off in their own world, separate from the HTML pages they resided in.

Google built a couple of systems to try to extend the browser to go beyond JavaScript. Native Client (NaCl) ran x86 (or ARM) programs in a secure sandbox, and Portable Native Client (PNaCl) did the same but using a kind of bytecode instead of x86 or ARM code. However, while Google championed these approaches other browser vendors largely rebuffed them. JavaScript was the lowest common denominator; it was the one thing that all browsers had to implement, so it was felt that it was better to make JavaScript better than to try to invent a whole new system.

As JavaScript became faster, the browser also became more capable. WebGL, for example, exposed hardware accelerated 3D graphics to the JavaScript developer. New APIs, to give access to, for example, games controllers, webcams, and microphones, have all been developed, extending the scope of what JavaScript in the browser can do. Simultaneously, a range of JavaScript-based-but-not-actually-JavaScript was devised. Microsoft's TypeScript, for example, offers various language features that Microsoft thinks are useful for the development of large programs by large teams. But browsers don't have to support TypeScript: the TypeScript compiler produces regular JavaScript that can run in any browser.

This kind of wide-ranging usage led Microsoft's Scott Hanselman to dub JavaScript the *assembly language for the Web*, a sentiment largely shared by people such as Brendan Eich, who invented JavaScript, and Douglas Crockford, who invented JSON, widely used for JavaScript-based data interchange.

But the people clamoring for a bytecode for the browser never went away, and they were never entirely wrong about the perceived advantages. And now they're going to get their wish. WebAssembly is a new project being worked on by people from Mozilla, Microsoft, Google, and Apple, to produce a bytecode for the Web.

Below picture depicts how it's been getting so far.

![](/assets/post_images/WebAssemblyMilestones.JPG)


### Where it stands *Now*

WebAssembly is intended to be a portable bytecode that will be efficient for browsers to download and load, providing a more efficient target for compilers than plain JavaScript or even asm.js. For example, .NET bytecode. Wasm instructions operate on native machine types such as 32-bit integers, enabling efficient compilation. It's also designed to be extensible, to make it easy to add, say, support for SIMD instruction sets like SSE and AVX.

WebAssembly will include both a binary notation, that compilers will produce, and a corresponding text notation, suitable for display in debuggers or development environments. Early prototypes are already showing some of the expected advantages; the binary representation is 20 times faster to parse than the equivalent asm.js.

The people behind wasm have not forgotten that JavaScript is supported everywhere and wasm is currently not supported anywhere. Their plan is to fill the gap with a polyfill; a JavaScript script that will convert wasm to asm.js for those browsers that don't have native wasm support. Either the browser will interpret the wasm directly, or it will load the polyfill and execute the resulting asm.js. Native handling should be faster, but the polyfill means that a developer can be sure that a wasm program will always work.

wasm is still in the early stages of development. There's no formal standards body behind it, just an informal community group. The specifications aren't complete, and the high level design is still being decided. But with all four major browser engine makers working together, the future of wasm should be bright. And the JavaScript skeptics who have been crying out for a bytecode for so long will finally get their wish.

For WebAssembly to succeed we need two things:

- Browsers to support WebAssembly natively
- Compilers that emit WebAssembly

In March 2017, the WebAssembly Community Group reached consensus on the initial (MVP) binary format, JavaScript API, and reference interpreter. It defines a WebAssembly binary format, which is not designed to be used by humans, as well as a human-readable Linear Assembly Bytecode format that resembles traditional assembly languages. But WebAssembly is still a moving target, with a lot of developments going on.

There is work being done on other languages to be compiled to WebAssembly like Rust and Swift. Steve Sanderson also has an experimental project called Blazor that shows .Net being compiled to WebAssembly.

### What's *Ahead*

Not everyone writes their apps in JavaScript. Some apps or libraries are already written in other languages, and it would be nice to run them in the web browser, so people do that. Some people make web games using Unity. Lots of reasons that this happens. So you end up with emscripten, asm.js, delivering a minified JavaScript blob multiple megabytes in size, which must be parsed, compiled, and executed. WebAssembly reduces the size of the blob you have to deliver and makes the parsing, compiling steps much faster. You may not like that, but you don't have to write apps that way. You can still write JavaScript if that's what you like. WebAssembly delivers improvements for people who weren't writing JavaScript in the first place, or for people who wish they weren't writing JavaScript.

We may see a resurgence in *small C libraries* targeting WebAssembly. There is a growing trend of header-only libraries, especially the popular stb(https://github.com/nothings/stb#stb_libs) and there is a growing list [here](https://github.com/nothings/single_file_libs). There are tiny libraries appropriate for linking into a web based C application e.g. lodepng, for decoding PNG images, and miniz, for reading and extracting zip files. There is an enormous amount of code out there in C, C++ and other languages that people would love to use in web apps. Productivity apps such as Photoshop and CAD can be ported. They are an enormous part of the software industry and the browser is a great platform for distribution for many cases. Performance and code secrecy being two barriers that WebAssembly solves for them.

There are so many new possibilities WebAssembly opens. Imagine if every AAA game had a web demo, real support for peer to peer video that worked in several browsers and platforms.
What could editors in browsers be? Not just text, but any kind of office suite that exists on the local. Google Docs and Microsoft Office already do that but reduced download size and increased efficiency can be achieved going wasm way. Anything requiring a 3d canvas that was limited by JavaScript's speed would be benefitted immensely. Truly, the web has grown to a surprisingly powerful and complete platform, and possibilities are infinite and why not because:

> I have seen the future, it's in my browser.

Further the WebAssembly community group and contributors have plans to:

- Distill the design and spec interpreter repos into a single unified specification in the spec repo
- Propose a new charter for a W3C WebAssembly Working Group
- Graduate the WebAssembly LLVM backend from experimental to stable (and update Emscripten)
- Prototype additional WebAssembly integration into browser developer tools
- Start work on post-MVP features

------

### Further Reading
* [WebAssembly - A New Hope](https://pspdfkit.com/blog/2017/webassembly-a-new-hope/)
* [WebAssembly - Developers Guide](http://webassembly.org/getting-started/developers-guide/)
* [WebAssembly - Future Features](https://github.com/WebAssembly/design/blob/master/FutureFeatures.md)
* [WebAssembly - Roadmap](http://webassembly.org/roadmap/)
* [WebAssembly - Examples](https://github.com/mdn/webassembly-examples)
