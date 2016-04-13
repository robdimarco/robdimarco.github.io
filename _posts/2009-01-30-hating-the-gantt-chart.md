---
id: 85
title: Hating the Gantt Chart
date: 2009-01-30T07:36:33+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=85
permalink: /hating-the-gantt-chart/
categories:
  - Software Development
  - tools
tags:
  - project-management scheduling uncertainty
---
"Can you get me a plan for doing that".&nbsp; How often have people said that.&nbsp; More often than not they are looking for some sort of [Gantt Chart](http://en.wikipedia.org/wiki/Gantt_chart) that shows a list of tasks, completion dates, and dependencies.&nbsp; Gantt charts are very pretty&#8230;I&#8217;ve made plenty of them.

But they suck.&nbsp; And they suck because they convey a level of certainty that is completely unfounded.&nbsp; It&#8217;s pretty rare that we put a plan together where it turns out we actually understood:

  * What needed to be done
  * How long each task would take
  * What other distractions (vacation, crucial issues, etc.) would distract resources from full concentration on the project.

My hatred of Gantt charts stems from the way they make the schedule seem black and white.&nbsp; So what I want is a way to create a project plan that shows the levels of gray in the timing.&nbsp; So a task would not take 3 days, it would perhaps have the highest probability of being complete in 3 days, but there are non-zero probabilities that it would take 4, 5, 6 days to complete.&nbsp; I&#8217;d also say that the more tasks in the project and the more resources involved the higher the likelihood of missing tasks, pushing out the schedule even more.

The closest I have found to this is [FogBugz](http://www.fogbugz.com)&#8216;s [Evidence Based Scheduling](http://www.fogcreek.com/FogBugz/docs/60/topics/schedules/Evidence-BasedScheduling.html) that shows a probability of a release shipping.&nbsp; I like that it is based on data from developers, but I want to find a generalized way to represent this schedule outside of entering every task into Fogbugz.&nbsp;

Maybe there&#8217;s a product development idea in here somewhere&#8230;