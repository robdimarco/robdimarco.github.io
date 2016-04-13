---
id: 34
title: 'Java Native Access (JNA): What JNI should have been'
date: 2007-09-05T08:51:45+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/java-native-access-jna-what-jni-should-have-been/
permalink: /java-native-access-jna-what-jni-should-have-been/
categories:
  - Software Development
tags:
  - Software Development
---
Just saw the release of the [Java Native Access (JNA)](https://jna.dev.java.net/)&nbsp;library that allows easier access to native shared access.&nbsp; Instead of annoying JNI adapter code to integrate the libraries, you can now just define an interface and use the JNA classes to manage the translation between the Java and the native code.&nbsp; It&#8217;s easy to use and really makes it easier to both build and deploy your application.&nbsp; Instead of complicated Ant scripts to auto-generate and compile JNI code, you can just define your interfaces and build normally.&nbsp; It&#8217;s amazing it took so long for such a library to come out.