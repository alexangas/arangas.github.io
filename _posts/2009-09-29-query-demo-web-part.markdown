---
author: admin
comments: true
date: 2009-09-29 21:36:53+00:00
layout: post
slug: query-demo-web-part
title: Query Demo Web Part
wordpress_id: 254
categories:
- SharePoint
tags:
- CodePlex
- demo
- query
- SharePoint
- web part
---

A [question recently came up on Stack Overflow](http://stackoverflow.com/questions/1374137/how-to-query-data-in-sharepoint-from-multiple-lists/) about querying data from multiple SharePoint lists and binding the results to a GridView control. I realised there were some types of SharePoint queries I hadn't explored, and that it would be interesting to compare them and see how they work in a web part.

Hence, [I created the SPQueryDemo web part!](http://stackoverflow.com/questions/1374137/how-to-query-data-in-sharepoint-from-multiple-lists/1444416#1444416) It tests and displays results from the following types of queries:





  * For loop


  * GetListItems from Lists web service


  * SPQuery for SPList objects


  * SPSiteDataQuery for cross-site list queries


  * CrossListQueryInfo for cached cross-site list queries


  * PortalSiteMapProvider for SharePoint Server publishing sites only



[Download it from CodePlex.](http://spquerydemo.codeplex.com/)

I've also used this project to try out using user controls for output rather than the traditional dynamic creation of controls in a SharePoint web part. This has been attempted with an MVP-style pattern which I'd really appreciate any feedback on.

In the future I'd like to [add more functionality](http://spquerydemo.codeplex.com/WorkItem/List.aspx), particularly for different query options and types of queries. There is also an annoying bug when changing the query type where the page needs to be refreshed before changes take affect. If anyone would like to help or just check out the code, please [download the source](http://spquerydemo.codeplex.com/SourceControl/ListDownloadableCommits.aspx) and hack away! You can also contact me via this blog post or [via Twitter](http://twitter.com/alexangas).

