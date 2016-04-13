---
id: 164
title: ActiveRecord default_scope is an Anti-Pattern
date: 2012-02-11T11:32:58+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=164
permalink: /activerecord-default_scope-is-an-anti-pattern/
aktt_notify_twitter:
  - yes
aktt_tweeted:
  - 1
categories:
  - Software Development
---
## TL;DR

Active Record&#8217;s _default_scope_ should be renamed to something like _always\_prepend\_scope_ to better describe what the method does.

## The Inspiration

The other day, ["default_scope" is an anti-pattern](https://twitter.com/#!/jamis/status/167021588951212034) came across my twitter feed.&nbsp; Having been burned by this issue I get the sentiment.

## The Problem with default_scope

Let&#8217;s say I have a simple class like the following (stolen from the [default_scope](http://api.rubyonrails.org/classes/ActiveRecord/Scoping/Default/ClassMethods.html#method-i-default_scope) documentation)



Now let&#8217;s look at some sample ruby calls and the SQL they generate



When I run _Article.all_ the SQL executed is _SELECT "articles".* FROM "articles" WHERE "articles"."published" = &#8216;t&#8217;_.&nbsp; Great, the default scope has been used and I only see published articles.

But let&#8217;s say now I want to find all articles with the title &#8216;a&#8217; REGARDLESS of their published status.&nbsp; My initial thought was that _Article.where(title: "a")_ would work because my _where_ would override the default _where.&nbsp;_ But as we can see, the SQL is now _SELECT "articles".* FROM "articles" WHERE "articles"."published" = &#8216;t&#8217; AND "articles"."title" = &#8216;a&#8217;.&nbsp;_ The where statement that I added does not override the where of the default scope, it appends it.&nbsp; OK, I find that to be unexpected, but to be fair, the documentation says:

_&nbsp;&nbsp;&nbsp; &#8216;Use this macro in your model to set a default scope for all operations on the model&#8217;_

So punish me with 50 lashes with a wet noodle for not reading documentation.&nbsp; But noodle flagellation (aside: I hope this blog post eventually ranks high in searching for that phrase) doesn&#8217;t write code.&nbsp; So how do I get around the default\_scope.&nbsp; This is the real problem is that it is REALLY HARD AND UGLY to now form an arel statement that removes the default\_scope.&nbsp; The best way I have found to do it is to call the protected, thinly documented, method [_with\_exclusive\_scope_](http://api.rubyonrails.org/classes/ActiveRecord/Scoping/ClassMethods.html#method-i-with_exclusive_scope) from an instance_eval block on an ActiveRecord::Base object (to get around the protected part).&nbsp; BLAH!!!

## An Anti-Pattern?

In my mind, this fits right into the [Anti-Pattern](http://en.wikipedia.org/wiki/Anti-pattern) criteria.&nbsp; At first blush, the functionality seems useful but as time unfolds, it leads to ugliness and bad consequences.

But the real problem is not with the functionality, rather that default_scope is deceptively named.&nbsp;

The [Webster&#8217;s definition for default](http://www.merriam-webster.com/dictionary/default):

<div class="sblk">
  <div class="scnt">
    <span class="ssens">&nbsp;&nbsp;&nbsp;&nbsp; </span><em><span class="ssens">a <strong>:</strong> a selection made usually automatically or without active consideration due to lack of a viable alternative <span class="vi"><remained the club&#8217;s president by default></span> </span> <span class="ssens"> <span class="break"> </span>b <strong>:</strong> a selection automatically used by a computer program in the absence of a choice made by the user </span></em>
  </div>
</div>

So in my mind, when I set the default scope it is the scope that should be used if I do not make an explicit choice.&nbsp; But once I make an explicit choice, like when I say _where(title: "a")_ then the default should NO LONGER be used.

Because of the inappropriate naming, the developer, when he first uses default_scope, does not realize what he is setting himself up for.&nbsp;Perhaps renaming the method to something like _always\_prepend\_scope_ would make developers realize what is happening and make their code easier to manage.

## UPDATED: Discussion

Chap Ambrose [brought up using unscoped](https://twitter.com/#!/chapambrose/status/168373769822412801).&nbsp; I&#8217;ve tried that in the past, you need to remember to use it in a block Article.unscoped{Article.where(title: &#8216;a&#8217;)} rather than just straight up Article.unscoped, which I would always screw up.&nbsp; Secondly, I did not see unscoped out on http://api.rubyonrails.org so I wasn&#8217;t sure if that was still appropriate.&nbsp; Finally, I still think it holds up as an anti-pattern as you initially think the default_scope will make your life easier, but it instead leads to having to put your arel in these unscoped blocks.&nbsp; Still ugly.