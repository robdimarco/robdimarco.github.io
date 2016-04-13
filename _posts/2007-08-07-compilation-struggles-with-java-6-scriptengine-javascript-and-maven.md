---
id: 29
title: Compilation Struggles with Java 6 ScriptEngine, JavaScript, and Maven
date: 2007-08-07T23:13:33+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/compilation-struggles-with-java-6-scriptengine-javascript-and-maven/
permalink: /compilation-struggles-with-java-6-scriptengine-javascript-and-maven/
categories:
  - Software Development
tags:
  - Java
  - Software Development
---
One of the projects I am currently on is to create a Java based headless browser that I can easily control programmatically.&nbsp; I have wanted this for a couple of reasons over the years, one for navigating websites when trying to scrape information and secondly for use in automated functional testing.&nbsp; I love the [Selenium IDE](http://www.openqa.org/selenium-ide/)&nbsp;but I find that it is impossible to automate your Selenium scripts into an automated build because it requires an actual browser (e.g. Firefox, Internet Explorer, etc.) to be started and executed each time.&nbsp; So I want a headless browser that will do navigation, manage the DOM model of the page, and execute JavaScript to handle AJAX calls.&nbsp; (SIDENOTE:&nbsp; I am planning on open sourcing this and am waiting for approval from SourceForge to move the code out there).

To support JavaScript, I wanted to just use the [ScriptEngineManager](http://java.sun.com/javase/6/docs/api/javax/script/ScriptEngineManager.html)&nbsp;found in Java 6 to execute JavaScript.&nbsp; Well, one problem that I found is that it is a real pain to get at objects that have been created in an executed script.&nbsp; Consider the following JavaScript example:

> **sample.js**
> 
> var foo = [&#8220;a&#8221;,&#8221;b&#8221;];

To execute that in Java 6, you would do the following:

ScriptEngineManager sm = new ScriptEngineManager();  
ScriptEngine e = sm.getEngineByExtension(&#8220;js&#8221;); 

String script = readFromFile(&#8220;sample.js&#8221;);

e.eval(script);

OK, so far so good.&nbsp; Now let&#8217;s say you want to retrieve the value of&nbsp;_foo_ from the ScriptEngine environment and iterate over the values.&nbsp; The ScriptEngine class has a [get](http://java.sun.com/javase/6/docs/api/javax/script/ScriptEngine.html#get(java.lang.String)) method, however, this returns an Object of type _sun.org.mozilla.javascript.internal.NativeArray_, not a Java Array.&nbsp; So to access values, you will need to cast the Object to a NativeArray.&nbsp; Then, as you iterate the array, you might have to cast each value to a NativeObject as the get method on NativeArray only returns an object.

> NativeArray a = (_sun.org.mozilla.javascript.internal.NativeArray_)e.get(&#8220;foo&#8221;);
> 
> for (int i = 0; i < a.getLength(); i++) {
> 
> &nbsp; System.out.println(a.get(i, a));
> 
> }

Now your code is relying on a Sun internal class and not a base JDK class.&nbsp; The appropriate classes exist in the $JAVA_HOME/lib/rt.jar file.&nbsp; Unfortunately, this jar&nbsp;file is NOT&nbsp;used by default&nbsp;when running javac.&nbsp; So to get my code to compile, I had to use the rt.jar in my bootclasspath.&nbsp;&nbsp;Since I&nbsp;am using [Maven 2.0](http://maven.apache.org)&nbsp;as my build system, I had to add the following to the pom.xml where I am defining my compiler

> <plugin>  
> &nbsp; <artifactId>maven-compiler-plugin</artifactId>  
> &nbsp; <configuration>  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <source>1.6</source>  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <target>1.6</target>  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <compilerArguments>  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <verbose />  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <bootclasspath>${java.home}/lib/rt.jar</bootclasspath>  
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </compilerArguments>  
> &nbsp; </configuration>  
> </plugin>

This put the rt.jar into the bootclasspath when building and allowed my code to compile.&nbsp; What I had hoped would be a simple task turned out to be a real ordeal.

Using the sun.org.mozilla.javascript.internal classes is very, very&nbsp;ugly and has the added distraction that it is hard to build and will be hard to maintain.&nbsp; I do not see a way around the problem and I have found&nbsp;other people having [similar problems working with the scripting API](http://www.mcqueeney.com/roller/page/tom?entry=returning_from_ruby_or_javascript).&nbsp; If anyone has a better approach, I am all ears.