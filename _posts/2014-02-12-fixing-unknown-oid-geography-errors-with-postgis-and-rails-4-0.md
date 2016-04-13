---
id: 173
title: 'Fixing unknown OID: geography errors with PostGis and Rails 4.0'
date: 2014-02-12T07:36:47+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=173
permalink: /fixing-unknown-oid-geography-errors-with-postgis-and-rails-4-0/
aktt_notify_twitter:
  - yes
categories:
  - Software Development
tags:
  - postgis
  - rails 4.0
---
Recently, when migrating a large Rails application to Rails 4.0 that used [PostGis](http://postgis.net),

I kept getting warnings like:

`unknown OID: geography(1838124)`

Thanks to 

<http://gray.fm/2013/09/17/unknown-oid-with-rails-and-postgresql/>

I just added a file config/initializers/postgres_oids.rb with content:

``Recently, when migrating a large Rails application to Rails 4.0 that used [PostGis](http://postgis.net),

I kept getting warnings like:

`unknown OID: geography(1838124)`

Thanks to 

<http://gray.fm/2013/09/17/unknown-oid-with-rails-and-postgresql/>

I just added a file config/initializers/postgres_oids.rb with content:

``