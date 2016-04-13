---
id: 71
title: Java Memory Management Improvement Proposal
date: 2008-10-30T07:25:45+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=71
permalink: /java-memory-management-improvement-proposal/
categories:
  - Java
  - Software Development
tags:
  - java memory
---
For the umpteenth time, I had to deal with the dreaded Java [OutOfMemoryError](http://java.sun.com/javase/6/docs/api/java/lang/OutOfMemoryError.html), this time when trying to run [Fisheye](https://www.atlassian.com/software/fisheye/) Subversion browser.&nbsp; My problem deals with a [known issue](http://jira.atlassian.com/browse/FE-624) in Fisheye where a background indexing task sometimes runs causes an OOM error.&nbsp; Of course, an OOM does not just effect the indexing task, it can also impact other operations unrelated to the task with the memory leak.&nbsp; While diagnosing this problem, it got me thinking about how this could be better managed.

## The Problem

&nbsp;The JVM defines a single heap for all objects created across all threads in the java process.&nbsp; A consequence of this is that if one processing thread has a memory leak, any thread on the JVM may start suffering from OOM errors.&nbsp; However, not all threads are equally important to my application.&nbsp; I may not mind too much if a batch processing thread fails with an OOM but I may care very much if the lack of memory causes all of my Tomcat threads to no longer be able to process web requests.

## Proposed Solution

What I would like is to be able to segment my heap so that I can dedicate portions of the heap (either by percentage of the total heap or by absolute number of bytes) to a specific set of work.&nbsp; That way if an OOM occurs, I can contain its impact to a certain set of threads while other threads continue processing.&nbsp; I would see this being configured as JVM runtime -X arguments, something like <span class="Code">-XHeapSegment:Name=MyMemHeap,Size=25%,Thread=<ThreadNameRegex></span>

I could see the partitioning being relative (by percentage) or absolute (total bytes allocated).&nbsp; I could also see linking it to ThreadGroups as opposed to just Threads.

## Some Questions

  * Do others see value in this proposal?
  * I am not an expert at JVM internals.&nbsp; Is there a fundamental reason this would not work?
  * Any other suggestions?
  * Should we allow this segmentation to be defined at compile-time as well through annotations?

## Notes

  * For the purposes of the problem I defined, I do not have a requirement that the young generations also be segmented.&nbsp; However, if it is impractical to keep a single set of young generations and segmented older generations, I can deal with having segmentation in the young generations as well.
  * Obviously, this would in no way address all OutOfMemoryErrors.&nbsp; We can still look forward to running out of PermGenSpace