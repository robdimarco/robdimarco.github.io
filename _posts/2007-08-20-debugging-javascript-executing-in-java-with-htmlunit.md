---
id: 31
title: Debugging JavaScript Executing in Java with HtmlUnit
date: 2007-08-20T16:05:54+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/debugging-javascript-executing-in-java-with-htmlunit/
permalink: /debugging-javascript-executing-in-java-with-htmlunit/
categories:
  - Software Development
tags:
  - Java
  - Open Source
  - Software Development
---
For the past few days, I have been doing a little work with embedded JavaScript within the [HtmlUnit](http://htmlunit.sourceforge.net)&nbsp;project.&nbsp; HtmlUnit is a really cool project that is a headless web browser written in Java that is really useful if you are trying to do automated testing or web scraping and want to simulate how a real browser works.&nbsp; One of the neat features is that it supports JavaScript execution within the HTML page so scripts that modify the DOM can be supported.&nbsp; One site that I was trying to automate a script for uses the [YUI](http://developer.yahoo.com/yui/) JavaScript libraries and I was having some problems with HtmlUnit, so I volunteered to have a shot at trying to get all of the unit tests to pass.

The hardest part of the development has been trying to debug why the hell all of the scripts have been failing.&nbsp; Most of the problems seem to fall into one or more of a few categories:

  * Hacks in the YUI libraries to deal with the fact that IE and Firefox both operate differently and NEITHER is fully compliant with the DOM specification.&nbsp; That has made for lots of fun times.&nbsp; I have found a certain irony that I am cursing the Yahoo developers for the hacks that they wrote while probably cursing 
  * Bugs with the [Rhino JavaScript](http://developer.mozilla.org/en/docs/Rhino) libraries.&nbsp; For example, I could not even start the YUI tests until I upgraded to 1.6R6.&nbsp; Also, I&nbsp;think I found a&nbsp;subtle bug&nbsp;that is causing one of the unit tests to fail related to the&nbsp;_for&#8230;in_ syntax, but it has been a bitch to reproduce.
  * No integrated debugging of the JavaScript code.&nbsp; The only way I can diagnose what is really breaking is by liberal use of the alert() method and then a review of the output.&nbsp; Hopefully this will change in [Rhino 1.7](http://developer.mozilla.org/en/docs/New_in_Rhino_1.7R1).&nbsp; Huge problem.
  * Unhelpful error messages.&nbsp; For example, I was getting a NullPointerException being thrown from SOMEWHERE in a script, but the stack trace was lost when it was transformed to a ScriptException.&nbsp; The only way I was able to debug the problem was to put a breakpoint on the constructor for NullPointerException and then look at the runtime stack when it was thrown, then grep through the Yahoo JavaScript to try to figure out where the problem could possibly be.

So if you ever having to work with the implementation

  * Make sure you are using a good IDE with a debugger.&nbsp; I cannot stress this enough.&nbsp; I have had to put breakpoints all over the damn place.&nbsp; And integrate as much of the source code around as you can and integrate it with the debugger.
  * If you are using Java 6, I would STRONGLY RECOMMEND NOT USING THE DEFAULT SCRIPTING IMPLEMENTATIONS!!!&nbsp; I have now worked with both the JRuby and JavaScript libraries.&nbsp; It is way easier to upgrade versions of your libraries to work around bugs if you are using the standalone implementations rather than the versions built into the JVMs.&nbsp; By the way, how much fun will it be when a later version of Java comes out with new scripting libraries that I am sure will not be 100% backwards compatible.
  * Take Mozilla, MSDN, and especially W3C reference documents with a grain of salt.&nbsp; They may not fully describe the behavior that you are seeing in the browser.