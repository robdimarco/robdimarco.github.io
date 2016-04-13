---
id: 131
title: Solr Sorting Tidbit
date: 2011-12-30T14:32:41+00:00
author: Rob Di Marco
layout: post
guid: http://www.innovationontherun.com/?p=131
permalink: /solr-sorting-tidbit/
aktt_notify_twitter:
  - yes
epigraph:
  - "I recently had a devil of a time getting Solr search results ordering to the client's specification.  Hopefully I can help someone out who may be in the same boat or maybe someone will suggest a better way."
aktt_tweeted:
  - 1
categories:
  - Software Development
tags:
  - solr
  - sunspot
---
The basic requirement was a fuzzy search on a series of business names and locations using a [Levenshtein](http://en.wikipedia.org/wiki/Levenshtein_distance) fuzzy string to handle typos, with results being returned with those whose name started with the query term displayed first followed by others that may have matched.&nbsp;

For our purposes, let&#8217;s say I had the following data set:

<table width="586" height="100" cellspacing="1" cellpadding="1" border="1">
  <caption><strong>Example Data Set</strong></caption> <tr>
    <td>
      <strong>ID</strong>
    </td>
    
    <td>
      <strong>Name</strong>
    </td>
    
    <td>
      <strong>Location</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      1
    </td>
    
    <td>
      Boone Moving Service
    </td>
    
    <td>
      Raleigh, NC
    </td>
  </tr>
  
  <tr>
    <td>
      2
    </td>
    
    <td>
      Bone&#8217;s Moving
    </td>
    
    <td>
      New York, NY
    </td>
  </tr>
  
  <tr>
    <td>
      3
    </td>
    
    <td>
      Boone Moving
    </td>
    
    <td>
      Los Angeles, CA
    </td>
  </tr>
  
  <tr>
    <td>
      4
    </td>
    
    <td>
      Jimmy&#8217;s Movers
    </td>
    
    <td>
      Boone, NC
    </td>
  </tr>
</table>

If a user was to search using the phrase "Boone Moving", we would expect all of these records to match because the two terms in the phrase "Boone" and "Moving" should match a term in either the Name or Location fields.

My application is built in Rails and I am using the [Sunspot](http://outoftime.github.com/sunspot/) gem for doing searches. Sunspot gives us some nice functionality including a nice DSL for indexing and searching for ActiveRecord model objects.&nbsp; I was using the [default schema.xml from Sunspot](https://github.com/sunspot/sunspot/blob/master/sunspot_solr/solr/solr/conf/schema.xml), so my initial work looked something like:



So my first question, why did we only get 2 results instead of all four. Well, the default Solr parser uses the Lucene parser which [uses Levenshtein for fuzzy searches](http://lucene.apache.org/java/2_9_1/queryparsersyntax.html#Fuzzy%20Searches).&nbsp;&nbsp; However, as I mentioned, I am using Sunspot, which uses the [dismax](http://wiki.apache.org/solr/DisMaxQParserPlugin) parser and dismax does not support fuzzy searching like this.&nbsp; So I changed my code to manually set the Solr parameters to sort on



Let&#8217;s dig into this code a little bit.&nbsp; In the custom_search method, I am using Sunspot&#8217;s search method, but instead of setting up keywords, I am manually setting up the Solr parameters.&nbsp; I break up a search query into tokens and then require that each token be found in at least one field.&nbsp; The "~" appended to each token tells Lucene to allow for fuzzy searching on this term.&nbsp; The result is that this matches all of our records.&nbsp; At this point, it is sort of ugly, I&#8217;m shortcutting some Sunspot functionality, but not too bad.

Now on to the next problem, sorting.&nbsp; The client requirements for the searching was that we want to give priority to those businesses whose names start with the query terms.&nbsp; So if I searched for "Boone Moving", I would want the businesses named "Boone Moving" and "Boone Moving Services" to show up higher than "Jimmy&#8217;s Movers" located in Boone.

In the default schema.xml, the name_text field uses the _[StandardTokenizerFactory](http://wiki.apache.org/solr/AnalyzersTokenizersTokenFilters#solr.StandardTokenizerFactory)_ which breaks up our names into individual tokens based on word boundary rules.&nbsp; But in order to search on the whole term, we do not want to have terms broken up.&nbsp; So, the first thing that I needed was to create a field that is not tokenized at all.&nbsp; To do this, we want to use a [The basic requirement was a fuzzy search on a series of business names and locations using a [Levenshtein](http://en.wikipedia.org/wiki/Levenshtein_distance) fuzzy string to handle typos, with results being returned with those whose name started with the query term displayed first followed by others that may have matched.&nbsp;

For our purposes, let&#8217;s say I had the following data set:

<table width="586" height="100" cellspacing="1" cellpadding="1" border="1">
  <caption><strong>Example Data Set</strong></caption> <tr>
    <td>
      <strong>ID</strong>
    </td>
    
    <td>
      <strong>Name</strong>
    </td>
    
    <td>
      <strong>Location</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      1
    </td>
    
    <td>
      Boone Moving Service
    </td>
    
    <td>
      Raleigh, NC
    </td>
  </tr>
  
  <tr>
    <td>
      2
    </td>
    
    <td>
      Bone&#8217;s Moving
    </td>
    
    <td>
      New York, NY
    </td>
  </tr>
  
  <tr>
    <td>
      3
    </td>
    
    <td>
      Boone Moving
    </td>
    
    <td>
      Los Angeles, CA
    </td>
  </tr>
  
  <tr>
    <td>
      4
    </td>
    
    <td>
      Jimmy&#8217;s Movers
    </td>
    
    <td>
      Boone, NC
    </td>
  </tr>
</table>

If a user was to search using the phrase "Boone Moving", we would expect all of these records to match because the two terms in the phrase "Boone" and "Moving" should match a term in either the Name or Location fields.

My application is built in Rails and I am using the [Sunspot](http://outoftime.github.com/sunspot/) gem for doing searches. Sunspot gives us some nice functionality including a nice DSL for indexing and searching for ActiveRecord model objects.&nbsp; I was using the [default schema.xml from Sunspot](https://github.com/sunspot/sunspot/blob/master/sunspot_solr/solr/solr/conf/schema.xml), so my initial work looked something like:



So my first question, why did we only get 2 results instead of all four. Well, the default Solr parser uses the Lucene parser which [uses Levenshtein for fuzzy searches](http://lucene.apache.org/java/2_9_1/queryparsersyntax.html#Fuzzy%20Searches).&nbsp;&nbsp; However, as I mentioned, I am using Sunspot, which uses the [dismax](http://wiki.apache.org/solr/DisMaxQParserPlugin) parser and dismax does not support fuzzy searching like this.&nbsp; So I changed my code to manually set the Solr parameters to sort on



Let&#8217;s dig into this code a little bit.&nbsp; In the custom_search method, I am using Sunspot&#8217;s search method, but instead of setting up keywords, I am manually setting up the Solr parameters.&nbsp; I break up a search query into tokens and then require that each token be found in at least one field.&nbsp; The "~" appended to each token tells Lucene to allow for fuzzy searching on this term.&nbsp; The result is that this matches all of our records.&nbsp; At this point, it is sort of ugly, I&#8217;m shortcutting some Sunspot functionality, but not too bad.

Now on to the next problem, sorting.&nbsp; The client requirements for the searching was that we want to give priority to those businesses whose names start with the query terms.&nbsp; So if I searched for "Boone Moving", I would want the businesses named "Boone Moving" and "Boone Moving Services" to show up higher than "Jimmy&#8217;s Movers" located in Boone.

In the default schema.xml, the name_text field uses the _[StandardTokenizerFactory](http://wiki.apache.org/solr/AnalyzersTokenizersTokenFilters#solr.StandardTokenizerFactory)_ which breaks up our names into individual tokens based on word boundary rules.&nbsp; But in order to search on the whole term, we do not want to have terms broken up.&nbsp; So, the first thing that I needed was to create a field that is not tokenized at all.&nbsp; To do this, we want to use a](http://wiki.apache.org/solr/AnalyzersTokenizersTokenFilters#solr.KeywordTokenizerFactory) and copy our business name into that field.



With this new schema.xml, we need to restart Solr and reindex all data to get the field indexed.&nbsp; Now we can tweak our custom_search method to use a highly weighted prefix query to boost the score for businesses with an exact match of the name.



Success!&nbsp;&nbsp; The key change that we have added an OR clause _<span class="sx">(_query_:"{!prefix f=name_untokenized v=$qq}")^10 </span>_<span class="sx">and an additional parameter into our hash <em>:qq=>query.downcase</em>.&nbsp; This new clause tells Solr to give </span><span class="sx">a big boost to any results where the term matches the untokenized version of our name.&nbsp; This is somewhat similar to the <a href="http://wiki.apache.org/solr/DisMaxQParserPlugin#bq_.28Boost_Query.29">boost query option in the dismax parser</a></span><span class="sx">.&nbsp; Because we are using this as an OR, records that do not match this prefix query are not excluded, but those that do match get a big boost.&nbsp; This results in a successful ordering of the records.<br /> </span>