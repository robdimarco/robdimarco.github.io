---
id: 46
title: The Power of GIT
date: 2008-04-03T18:10:20+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=46
permalink: /the-power-of-git/
categories:
  - Software Development
tags:
  - subversion
  - version control
---
Started working with git for the first time last week and I am really impressed at how powerful the tool is.&nbsp; I&#8217;ve been interested in using a distributed version control system like git, darcs, or mercurial for a while, but I have been turned off on actually making the move because of the lack of integration with these version control systems and other software (bug tracking, automated build software, etc.).&nbsp; Also, as a manager, I did not want to have to retrain a whole team of developers on a new version control system.

But enter [git-svn](http://www.kernel.org/pub/software/scm/git/docs/git-svn.html) which allows me to use git locally and Subversion for the distributed repository.&nbsp; So now I get the best of both worlds, I can make my local branches and commits, but I don&#8217;t have to retrain my team or worry about integration as we still have all of the Subversion interfaces for interacting with the repository.

Using git-svn is pretty simple.&nbsp; I had to install on CentOS, Max OS X, and Ubuntu and git was available through yum/apt-get/Macports.&nbsp; To check out was just:

<span class="Code">git svn clone http://svn.automattic.com/wordpress/ -ttags -bbranches -Ttrunk</span>

This command will check out all revisions from Subversion and keep a copy of this locally.&nbsp; Because we download a copy for all revisions, it can take a long time to do this initial setup.

As for the -t/-T/-b, this is used by git-svn to associate SVN branches with git branches.&nbsp; This configures your git master branch to correspond with the wordpress trunk, but also gives you a git branch for each SVN branch if you want your changes to be tied to a branch rather than the trunk.

One the clone completes, I can use all of the git tools to my heart&#8217;s content.&nbsp; Local checkins, branches, easy merges, etc.&nbsp; If I need to refresh from Subversion, git will get all new changes from SVN, then replay my edits on top of it.

Committing changes back (assuming that you have permission) just requires:

<span class="Code">git svn dcommit </span>

All of your changes are now committed one at a time to the SVN repository.&nbsp; Very simple for me to get set up and run with.

# Digging Deeper&#8230;

As I started using git more, I realized how perfect it is for another big problem that I have, managing production configurations.&nbsp; For example, I have a common problem when managing Apache configurations.&nbsp; I have the following needs:

  * I use multiple conf files to manage my Apache installation (main httpd.conf, virtual host, SSL, etc.)
  * I want basic version control over the files to track changes over time
  * When trying to debug a problem, I often have to go into the files and tweak multiple debug settings (LogLevel, RewriteLogLevel, etc.).

&nbsp;What git does for me is lets me do is to create a branch that just contains my debug changes and lets me easily merge the master branch in with these changes.&nbsp; To do this, I create a debug branch that contains all of my changes needed

<span class="Code">git branch debug</span>

<span class="Code">git checkout -b debug</span>

..make changes&#8230;

<span class="Code">git commit</span>

<span class="Code">git checkout -b master</span>

&#8230; debug is now off&#8230;

.. Make changes and commit back to master&#8230;

<span class="Code">git commit</span>

&nbsp;..Now when I need to debug again

<span class="Code">git checkout -b debug && git merge master</span>

The merge command will sync the debug branch with the master branch and then will REPLAY the changes that I made to turn on debugging.&nbsp; When I finish I can turn off my debugging by

<span class="Code">git checkout -b master</span>

&nbsp;I see using this not just to manage logging but in other configuration management problems like:

  * Managing database connection information
  * Configuring network arrangements