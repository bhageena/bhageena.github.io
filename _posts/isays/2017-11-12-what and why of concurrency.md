---
layout: post
title: "What and Why of Concurrency"
categories: isays
modified:
excerpt: "Thinking about concurrency is vital to writing correct, scalable, and performant software. However, the successful application of concurrency is a perfect mix of theory and practice. Concurrency is a great way to get more stuff done faster. Due to limitations in heat dissipation technology, [Moore’s Law](https://en.wikipedia.org/wiki/Moore%27s_law) hit practical limits over a decade ago and the only way computing has progressed has been to cram more cores onto a die, and/or use more CPUs. And [Amdahl’s Law](http://en.wikipedia.org/wiki/Amdahl's_law) has ruled ever since"
tags: []
image:
  feature:
readtime: true
---

Concurrency is a great way to get more stuff done faster. Due to limitations in heat dissipation technology, [Moore’s Law](https://en.wikipedia.org/wiki/Moore%27s_law) hit practical limits over a decade ago and the only way computing has progressed has been to cram more cores onto a die, and/or use more CPUs. And [Amdahl’s Law](http://en.wikipedia.org/wiki/Amdahl's_law) has ruled ever since.

Concurrency, though, is everywhere, and there exists a large class of systems that we don’t traditionally think of as *concurrent* — such as the interactions between a client and a server, single-threaded applications running on a multi-user system, OS virtualization, shared datastores, and many more. Thinking about concurrency is vital to writing correct, scalable, and performant software. However, the successful application of concurrency is a perfect mix of theory and practice.

Concurrency is a vital aspect of beautiful software. For decades, concurrency was possible but difficult. Concurrent software was difficult to write, difficult to debug, and difficult to maintain. As a result, many developers chose the easier path and avoided concurrency. However, with the libraries and language features available for new releases of frameworks and languages, concurrency is much easier. Previously, concurrent programming was the domain of experts; these days, every developer can (and should) embrace concurrency.

## What is Concurrency

Concurrency is `doing more than one thing at a time`. A concurrent program is one with multiple control flows that overlap in time. Concurrency changes the semantics of a program such that the independent flows of control can observe this change. For example, a web server is a concurrent program and I can observe, as a client of that web server, that I don't need for one user's request to complete before mine can begin. It’s pretty obvious how concurrency is helpful. End-user applications use concurrency to respond to user input while writing to a database. Server applications use concurrency to respond to a second request while finishing the first request. You need concurrency any time you need an application to do one thing while it’s working on something else. Almost every software application in the world can benefit from concurrency.


## Different Forms of Concurrency

### Multithreading
Most developers hearing the term *concurrency* immediately think of *multithreading.* But there is important distinction between these two. Multithreading is a form of concurrency that uses multiple threads of execution. Multithreading literally refers to using multiple threads. Multithreading is one form of concurrency, but certainly not the only one. In fact, direct use of the low-level threading types has almost no purpose in a modern application; higher-level abstractions are more powerful and more efficient than old school multithreading.  

But Multithreading is not dead, Multithreading lives on in the thread pool, a useful place to queue work that automatically adjusts itself according to demand. In turn, the thread pool enables another important form of concurrency: *Parallel Processing*.

### Parallel Processing
Parallel Processing does lots of work by dividing it up among multiple threads that run concurrently. Parallel processing uses multithreading to maximize the use of multiple processors. Modern CPUs have multiple cores, and if there’s a lot of work to do, then it makes no sense to just make one core do all the work while the others sit idle. Parallel processing will split up the work among multiple threads, which can each run independently on a different core. Parallel processing is one type of multithreading, and multithreading is one type of concurrency.

### Asynchronous Programming
Asynchronous Programming is important in modern applications but currently, it's not widely used. It is a form of concurrency that uses futures or callbacks to avoid unnecessary threads. A future or promise is a type that represents some operation that will complete in the future. Asynchronous programming is centered around the idea of an asynchronous operation: some operation that is started that will complete some time later. While the operation is in progress, it does not block the original thread; the thread that starts the operation is free to do other work. When the operation completes, it notifies its future or invokes its completion callback event to let the application know the operation is finished. Asynchronous programming is a powerful form of concurrency, but until recently, it required extremely complex code. The async and await support in many languages makes asynchronous programming almost as easy as synchronous/nonconcurrent programming.

### Reactive Programming
Asynchronous programming implies that the application will start an operation that will complete once at a later time. Reactive programming is closely related to asynchronous programming, but is built on asynchronous events instead of asynchronous operations. Asynchronous events may not have an actual *start,* may happen at any time, and may be raised multiple times. One example is user input. Reactive Programming is a declarative style of programming where the application reacts to events. If you consider an application to be a massive state machine, the application’s behavior can be described as reacting to a series of events by updating its state at each event. This is not as abstract or theoretical as it sounds; modern frameworks make this approach quite useful in real-world applications. Reactive programming is not necessarily concurrent, but it is closely related to concurrency.

Usually, a mixture of techniques are used in a concurrent program. Most applications at least use multithreading (via the thread pool) and asynchronous programming. Feel free to mix and match all the various forms of concurrency, using the appropriate tool for each part of the application.

People often use *parallel* and *concurrent* interchangeably, but there is a subtle difference. In fact, they are related but quite distinct concepts.

##  What is Parallelism

When people hear the word concurrency they often think of parallelism, these two words have a very distinct meaning in the realm of engineering and computers, they might be used as synonyms outside this context but if we're talking about computers we have to be crystal clear what each one means. First let's talk concurrency, human beings, you and me, we're very concurrent, every day you wake up and you have a lot of stuff to do you got a shower you got to brush your teeth, you got to put on your clothes, you got to start your coffee. After that you get ready for work or school, you go to your classes, spend some time on your homework, you talk with your friends. Every day we have this big pile of stuff to do, there are hundred things and it's up to us to figure out how to get that done every single day. Each of these tasks that we have to do throughout the day may start and stop at different times. You brush your teeth first and then you put on your clothes. Usually as you go about your day, you slowly chip away at those hundred tasks. What we're really good at doing is multitasking, we'll be doing our homework we'll check our email real quick send a text message and go back to doing our homework.

The really important thing to remember about this multitasking and what we do on a day to day basis is that we can't do two things at exactly the same time. When you're brushing your teeth, you don't put on your clothes at the same time. Even though we're handling a lot of stuff and we're multitasking no two things actually happen at the same absolute time.

Our day to day stuff and our multitasking is really a good example of concurrency. Just remember there are some limits to this concurrency concept, you can probably handle hundred things to do in a day, but you probably couldn't handle ten thousand things to do unless you're a Superman.

Concurrency is the ability to work on a lot of tasks and have them all start and finish at different points of time but you can handle them all your head each day you have a hundred things to do. You're not going to do all of them in the morning but you'll probably complete them all by the time you go to bed, unless you're a slacker.

Concurrency is the composition of independently executing processes, while parallelism is the simultaneous execution of possibly related computations. Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once.

Parallelism is physically doing two or more things at exactly the same point in time or simultaneously if two physical tasks can execute at the same exact absolute time, they exhibit parallelism. Let's just take an example, human beings, you and me, we can't physically brush our teeth and put on our clothes at the same time unless really skilled or we have more than two arms. Still there are many tasks that can be done in parallel, but we are not really good at.

Parallelism is the act of taking a large job, splitting it up into smaller ones, and doing them at once. Concurrency is necessary for parallelism but not the other way around. If you alternate between cooking eggs and pancakes you are doing both concurrently. If you are cooking eggs while I am cooking pancakes, we are cooking concurrently and in parallel. Technically if you're cooking eggs and I am mowing the lawn we are also working in parallel, but since no coordination is needed in that case there's nothing to talk about.

Let's concretize the understanding by looking at real world examples of parallel, concurrent, and asynchronous programming:

Parallel programs distribute their tasks to multiple processors, that actively work on all of them simultaneously. In a product assembly line, multiple workers working on different parts of the product is a from of parallelism.  

Concurrent programs handle tasks that are all in progress at the same time, but it is only necessary to work briefly and separately on each task, so the work can be interleaved in whatever order the tasks require. A juggler at a fair juggling multiple balls is a form of concurrency.

An asynchronous program dispatches tasks to devices that can take care of themselves, leaving the program free do something else until it receives a signal that the results are finished. If you put your clothes in laundry machine and while waiting for it to get done, you pick the days newspaper and start reading -- this is asynchrony.


## Further Readings
[Evolution of concurrency in .net](http://joeduffyblog.com/2016/11/30/15-years-of-concurrency/)
[Concurrency is not Parallelism: Rob Pike at Waza 2012](https://player.vimeo.com/video/49718712)
[One Nice Explanation of concurrency](https://armstrongonsoftware.blogspot.in/2006/08/concurrency-is-easy.html)
