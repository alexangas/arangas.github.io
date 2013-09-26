---
author: admin
comments: true
date: 2011-09-29 20:15:19+00:00
layout: post
slug: developing-for-a-load-balanced-sharepoint-environment
title: Developing for a load balanced SharePoint environment
wordpress_id: 4
categories:
- SharePoint
tags:
- debugging
- multithreading
- SharePoint
---

If you are going to deploy to a load balanced environment, a big plus is being able to develop in one. Unfortunately a lot of us don't have that luxury! Here are some key points to help out.



# Managing state



Always rely on SharePoint's central store - the database. Don't try and store data on the file system as you will never be sure which server has what copy. Use lists for data that should be exposed to administrators and users. Use SPPersistedObject or the patterns & practices [Application Setting Manager](http://msdn.microsoft.com/en-us/library/ff798488.aspx) for application configuration.



# Caching



This is just another method of storing state, albeit one that's faster (yet limited in resources)! Be aware when using ASP.NET caching that every server will cache your data separately in its own copy of RAM. These will get out of sync resulting in inconsistencies that you will need to handle. You may find AppFabric Caching as [described by Wictor Wilén](http://www.wictorwilen.se/Post/Improve-performance-of-your-SharePoint-2010-applications-using-Windows-Server-AppFabric-caching.aspx) a good solution to this problem.



# Thread safety



Never store rich objects that aren't thread safe such as SPListItem in the cache or any other form of state. Aside from potentially taking up unnecessary space, these objects won't work properly once deserialized. Introducing caching also opens up your application to potential threading issues, as explained in "Understanding the Potential Pitfalls of Thread Synchronization" [in this best practices article](http://msdn2.microsoft.com/en-us/library/bb687949.aspx). You don't need to use [System.Threading](http://msdn.microsoft.com/en-us/library/system.threading.aspx) to be multithreading!



# Testing



Even if you can't develop in a load balanced environment, it really pays to test in one. A lot of situations just can't be reproduced otherwise! I strongly recommend to ensure your production environment always closely matches test in configuration (even if number of servers is unreasonable).



# Conclusion



In summary: always store data in the database to avoid problems with different servers accessing the same content and keep any caching simple and stateless. Oh, and be sure to thoroughly test!

