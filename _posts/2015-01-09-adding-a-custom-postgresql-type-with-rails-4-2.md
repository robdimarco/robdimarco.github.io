---
id: 178
title: Adding a custom Postgresql Type with Rails 4.2
date: 2015-01-09T15:03:37+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=178
permalink: /adding-a-custom-postgresql-type-with-rails-4-2/
categories:
  - Software Development
---
In one of my Rails applications, we are using the [PostGIS](http://postgis.net/) custom Postgres data type [GEOGRAPHY](http://postgis.net/docs/manual-1.5/ch04.html#PostGIS_Geography). When `ActiveRecord` stumbles across this data type, by default it does not know what to do with it.

Prior to Rails 4.1, there was a utility method on the [ActiveRecord::ConnectionAdapters::PostgreSQLAdapter::OID](https://github.com/rails/rails/blob/4-1-stable/activerecord/lib/active_record/connection_adapters/postgresql/oid.rb#L329) module called <code class="ruby">register_type</code>.

In Rails 4.2, this [method was removed](https://github.com/rails/rails/commit/4826a4ab348e59c3ac1f6c31fd73f93eabb91a3b), so there is no way to easily register a new type.

To get around this problem, I decided to monkey patch <code class="ruby">PostgreSQLAdapter</code>.

<pre><code class="ruby">

ActiveRecord::ConnectionAdapters::PostgreSQLAdapter.class_eval do
  def initialize_type_map_with_postgres_oids(m)
    initialize_type_map_without_postgres_oids(m)
    register_class_with_limit m, 
      'geography', 
      ActiveRecord::ConnectionAdapters::PostgreSQLAdapter::OID::Integer
  end
  alias_method_chain :initialize_type_map, :postgres_oids
end

</code></pre>