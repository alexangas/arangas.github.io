---
author: admin
comments: true
date: 2012-08-22 09:20:10+00:00
layout: post
slug: sql-server-index-tuning-entity-framework-wcf-data-services
title: SQL Server index tuning flavoured with Entity Framework and WCF Data Services
wordpress_id: 362
categories:
- .NET
- database
tags:
- database
- entity-framework
- performance
- wcf-data-services
---

I'm working on a particularly database intensive application at the moment. We use Entity Framework code first for our back-end database reads and writes, and have a WCF Data Services wrapper for REST read access from the UI.

Over the last few weeks as load has increased I've started noticing the occasional SQL deadlock and timeout on production. Suddenly yesterday we reached some sort of tipping point and were inundated with timeouts. The cause of most of these was found just by running SQL Profiler and watching some unnecessary queries spill down the screen (multiple functions inside a view, bad idea). Once the chaos was largely over, I started looking at whether our database indexes were actually useful and how to verify this with EF and WCF DS.


# Configuring SQL Profiler for Entity Framework


These are the SQL Profiler settings I found worked best to capture both EF and WCF DS queries (with Show All Columns ticked):



	
  * SP:StmtCompleted

	
  * SP:StmtStarting

	
  * SQL:BatchCompleted

	
  * SQL:BatchStarting


This combination allows you to see CPU time and how long statements took, as well as then letting you load the trace into Database Tuning Advisor (DTA). If you want to check for timeouts and deadlocks as well, make sure you include:

	
  * Deadlock graph

	
  * Lock:Deadlock

	
  * Lock:Deadlock Chain

	
  * Lock:Timeout (timeout > 0)


![](http://www.alexangas.com/blog/wp-content/uploads/2012/08/TraceProperties.png)

Back to the Database Tuning Advisor, it specifically [requires](http://msdn.microsoft.com/en-us/library/ms190957%28v=sql.105%29) SQL:BatchCompleted and SP:StmtCompleted. The details of how to get that running [is on MSDN](http://msdn.microsoft.com/en-us/library/ms178095%28v=sql.105%29). Set the SQL Profiler trace to output to a file (if you want to analyse on your local machine) or database table. Let it run for a few hours while the tasks you want to optimise are being performed by your users. If you're game you can run this on production for more accurate results, but a test environment is preferred provided you can recreate a typical load.


# Analyzing SQL Profiler results in the Database Tuning Advisor


I found the most useful things from the DTA are the SQL script of recommendations in combination with the "Index usage report (recommended)". These will get you 90% of the way to some quick optimizations. After running the DTA, save the SQL script from the Actions / Save Recommendations menu. Then switch over to the Reports tab, choose the report just mentioned, and then right-click on the table of results and click "Export To File" (this feature is not obvious!).

Copy both files over to your local machine. Open the script in a text editor, and use Excel to open the XML report. Sort by "PercentUsage" and look at how the indexes you already have compare with the DTA recommendations (that start with _dta_index). You can easily find its recommended indexes in the SQL script and adapt them to your needs - just give them good names! Also consider dropping the indexes that don't show up at all as each database write usually causes an update of the index which can take time and cause further timeout issues.


