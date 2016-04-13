---
id: 53
title: MapMyRide WordPress Plugin Released
date: 2008-07-15T01:23:32+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=53
permalink: /mapmyride-wordpress-plugin-released/
categories:
  - Software Development
tags:
  - mapmyhike
  - mapmyride
  - mapmyrun
  - plugin
  - widget
  - wordpress
---
I have been trying to use MapMyRide to track my workouts.&nbsp; While they say they will have a JSON feed soon, for now, all I could do is work with their RSS feed to put it up on my blog.

Originally, I hoped I could just use a standard WordPress RSS feed widget to show my workouts.&nbsp; Unfortunately, the feed from MapMyRide contains HTML.&nbsp; The RSS widget wisely escapes the HTML to avoid XSS.&nbsp; However, this makes the workout listings look very ugly.

So I have created a pluging that downloads the RSS feed, parses out the workout information, and shows your workouts.

You can [download the initial version](http://downloads.wordpress.org/plugin/mapmyride-workout-plugin.1.0.zip) of the plugin.&nbsp; Installation just requires you to unzip and put in your wp-content/plugins directory.&nbsp; Please let me know if you are using it and if there any other features needed.&nbsp; I know I still need to:

  * Protect agains XSS (split on the bold tags and then escape other HTML
  * Protect against bogus URLs, only MapMyRide.com should be used
  * Add more options to control what you actually allow to display.&nbsp; Not sure if I want my weight out there.