---
layout: post
title: How WebKit Works
description: "Though a lot has changed since halcyon days of webkit -- Chrome forked WebKit and started Blink, Opera adopted Chromium, node-webkit became nw.js  -- still Webkit empowers major share of [web browsers](https://en.wikipedia.org/wiki/List_of_web_browsers#WebKit-based) and has played vital role in the [evolution](https://upload.wikimedia.org/wikipedia/commons/7/74/Timeline_of_web_browsers.svg)
of browsers"
category: blog
comments: true
tags: "webkit rendering-engine"
excerpt: ""
share: true
readtime: true
---
### What is Webkit

Webkit is an open source rendering engine. Originally started as an engine for the Linux platform, later it was modified by Apple to support Mac and Windows.

Though a lot has changed since halcyon days of webkit -- Chrome forked WebKit and started Blink, Opera adopted Chromium, node-webkit became nw.js  -- still Webkit empowers major share of [web browsers](https://en.wikipedia.org/wiki/List_of_web_browsers#WebKit-based) and has played vital role in the [evolution](https://upload.wikimedia.org/wikipedia/commons/7/74/Timeline_of_web_browsers.svg)
of browsers.

#### Webkit2

In April 2010, to abstract the components that provide web rendering cleanly from their surrounding interface or application shell -- so that web content (JavaScript, HTML, layout, etc) lives in a separate process from the application UI -- Webkit was redesigned to form WebKit2.

### It's not a black box

WebKit seems to be a black box. It takes HTML, CSS, JS and a bunch of other assets, and gives us a webpage that looks and works so well.

>(HTML + JavaScript + CSS) -> **WebKit** -> Rendering of a web page

But inside lies a legacy and understanding internals of [Webkit](https://trac.webkit.org/browser/webkit/releases/WebKitGTK/webkit-2.17.3/Source) is pretty exciting.

### How it works: let's take a peek

As the name itself suggests, the responsibility of a rendering engine is well... *Rendering*, that is displaying the contents of the requested document on the browser screen.


Webkit gets the response of the HTTP request from the networking layer, usually in 8KB chunks, and performs the following tasks in order:

>
1.  Parsing HTML to construct the DOM tree
2.  Render tree construction
3.  Layout of the render tree
4.  Painting the render tree


Webkit starts parsing the HTML document and turns the tags to DOM nodes in form of a tree called the `Content Tree`. It also parses the style data in external CSS files or on-page style elements. This styling information (CSS Object Model) combined with the visual instructions in HTML markup (DOM) is used to form another tree, the `Render Tree`.

The render tree contains rectangles with visual attributes like color and dimensions. The rectangles are in the right order to be displayed on the screen.

After the construction of the render tree it goes through a `Layout` process. Giving each node the exact coordinates where they should appear on the screen. The next stage is painting - the render tree is traversed and each node is painted to pixels on the screen.

It's important to understand that this is a gradual process. For better user experience, Webkit tries to display contents on the screen as soon as possible. It does not wait until all HTML is parsed before starting to build and layout the render tree. Parts of the content gets parsed and displayed, while the process continues with the rest of the contents that keeps coming from the network.

The time required to perform render tree construction, layout and paint depends on the size of the document, the applied styles, and also the device it is running on: the larger the document, the more work the browser has; the more complicated the styles, the more time taken for painting.


#### Parsing HTML to construct the DOM tree

Webkit first parses HTML markup into a parse tree using HTML parser, but from the network it receives HTML markup in form of bytes.

So the process goes like this:

>Bytes -> Characters -> Tokens -> Nodes -> DOM

`Bytes` are converted into `Characters`, and with the help of Tokenizers, they are converted into `Tokens`, the Tree Builder then picks up tokens as `Nodes` and builds the `DOM` tree.


#### Render tree construction

While the DOM tree is being constructed, the browser constructs another tree, the render tree. This tree is of visual elements in the order in which they will be displayed. It is the visual representation of the document. The purpose of this tree is to enable painting the contents in their correct order.



#### Layout of the render tree

When the renderer is created and added to the tree, it does not have a position and size. Calculating these values is called layout or reflow. Render Tree is used to compute the layout of each visible element and serves as an input to the paint process that renders the pixels to screen. Optimizing each of these steps is critical to achieving optimal rendering performance.

HTML uses a flow based layout model, meaning that most of the time it is possible to compute the geometry in a single pass. Elements that come later ``in the flow`` typically do not affect the geometry of elements that are earlier ``in the flow``, so layout can proceed left-to-right, top-to-bottom through the document. There are exceptions though: e.g., HTML tables may require more than one pass.

The coordinate system is relative to the root frame. Top and left coordinates are used.

Layout is a recursive process. It begins at the root renderer, which corresponds to the `<html>` element of the HTML document. Layout continues recursively through some or all of the frame hierarchy, computing geometric information for each renderer that requires it.

The position of the root renderer is 0,0 and its dimensions are the viewport–the visible part of the browser window.
All renderers have a `layout` or `reflow` method, each renderer invokes the layout method of its children that need layout.

#### Painting of the render tree

The output of the layout process is a "box model," which precisely captures the exact position and size of each element within the viewport: all of the relative measurements are converted to absolute pixels on the screen.

In the painting stage, the render tree is traversed and the renderer's `paint()` method is called along with the computed styles and geometry to display each node in the render tree to actual pixels on the screen.  This step is often referred to as `painting` or `rasterizing`. Painting uses the UI infrastructure component.

------
