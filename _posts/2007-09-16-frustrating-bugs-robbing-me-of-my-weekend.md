---
id: 39
title: Frustrating Bugs Robbing Me of My Weekend
date: 2007-09-16T16:06:10+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/frustrating-bugs-robbing-me-of-my-weekend/
permalink: /frustrating-bugs-robbing-me-of-my-weekend/
categories:
  - Software Development
tags:
  - Software Development
---
This Tuesday, I am [presenting](/presentations/) at the [Philadelphia JBoss Users Group](http://www.pjbug.com) on using [Embedded JBoss](http://blogs.jboss.com/blog/bburke/2007/04/17/Embedded_JBoss_JBoss_without_the_Application_SErver.txt), a very cool way that allows you to do things like test EJBs using JUnit without having to install an application server.&nbsp; The slides are ready but I wanted to make sure my demos are running.&nbsp; So I go to run the tests and my first error is:

_Class not found: [Ljava.lang.String;_

What the hell?&nbsp; Of course I initially assume it is something I did wrong, then that it is something in the JBoss configuration.&nbsp; After much anguish and googling, it turns out there is an [awesome bug/feature in Java 1.6](http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=6434149) where the&nbsp;following code (which&nbsp;worked in Java 1.5)&nbsp;throws&nbsp;a ClassNotFoundException in Java 1.6.

&nbsp; public class test {

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; public static void main(String[] args) throws Exception {  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; String[] s = new String[] { &#8220;123&#8221; };  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; String clName = s.getClass().getName();  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; test.class.getClassLoader().loadClass(clName);&nbsp; // throws exception in JDK 1.6!  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }  
&nbsp; }

But the lameness did not stop there.&nbsp; The next issue that popped up was a NullPointerException when trying to read a file.&nbsp; Turns out there is a bug on Windows XP with directory names involving spaces.&nbsp; I was running it off of my desktop, c:\Documents and Settings\rdimarco\Desktop.&nbsp; Moving the application to the c:\jboss directory solved my problems.&nbsp; I&#8217;m not sure if it is a JDK or JBoss bug, but at this point I&#8217;m just frustrated.