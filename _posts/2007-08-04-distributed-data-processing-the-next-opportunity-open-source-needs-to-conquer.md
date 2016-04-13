---
id: 28
title: 'Distributed Data Processing: The next opportunity Open Source needs to conquer?'
date: 2007-08-04T15:41:55+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/distributed-data-processing-the-next-opportunity-open-source-needs-to-conquer/
permalink: /distributed-data-processing-the-next-opportunity-open-source-needs-to-conquer/
categories:
  - Software Development
tags:
  - Open Source
  - Software Development
  - Technology Business
---
Just ran across a couple of articles on distributed data processing.&nbsp; InfoQ is discussing the need for a [new paradigm for scaling processing](http://www.infoq.com/news/2007/08/scalability-patterns) at the same time development on an open source solution is progressing as [Yahoo! seems to be gearing up development](http://developer.yahoo.net/blog/archives/2007/07/yahoo-hadoop.html)&nbsp;on [Hadoop](http://lucene.apache.org/hadoop/)&nbsp;([Tim O&#8217;Reilly&#8217;s take](http://radar.oreilly.com/archives/2007/08/yahoos_bet_on_h.html)).&nbsp; When you get to a certain size of data, you have to think about solving problems outside of the traditional application server talking to database server way.&nbsp; If you want to complex processing against millions to billions of entities, your data access time on the traditional database system is way too slow no matter how many boxes or how fast your processing logic is.&nbsp; More and more organizations will start to face this problem, and there is a definite need for an open-source&nbsp;distributed processing framework that can become a standard and allow for developers to start building tools on top of it.&nbsp; Maybe it will be the Hadoop library, maybe it will be someone else. 

At my previous employer, we (by we, I mean a bunch of other smart people on my team) have built out&nbsp;a framework to manage this sort of distributed programming.&nbsp; I advocated strongly that the organization should open source the project using many of the [reasons discussed earlier](http://www.innovationontherun.com/open-source-software-as-a-marketing-platform-for-hiring/).&nbsp; Now that I have left, I hope someone can pick up the torch and get the MapReduce project released.