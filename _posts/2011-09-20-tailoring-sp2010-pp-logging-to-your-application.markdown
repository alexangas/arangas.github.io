---
author: admin
comments: true
date: 2011-09-20 20:02:41+00:00
layout: post
slug: tailoring-sp2010-pp-logging-to-your-application
title: Tailoring SP2010 p&p logging to your application
wordpress_id: 297
categories:
- SharePoint
tags:
- logging
- patterns-and-practices
- SharePoint
---

Back in our [last post](http://www.alexangas.com/blog/?p=289), we saw an intro to the logging power found in the SP 2010 patterns & practices library. Now, let's make it our own.

**Defining your own areas and categories**

The out-of-the-box behaviour is fine, but it's much nicer for both end user and developer to see the actual application name causing the exception, rather than the generic "Patterns and Practices". No problem, you can define your own areas and categories! What does this mean? Examine a ULS log file and you'll see at the top a series of column headers. "Area" is typically used for the a major piece of functionality or application name. "Category" is some component of that functionality. For logging to the Event Viewer, the Source column is used for "Area", and "Category" is the first line of the log entry.

**The many ILogger methods**

Before continuing, let's examine how we use a custom area and category when calling the ILogger. If you have a look at its interface, you'll find several variations of two methods. **LogToOperations** writes to both the ULS trace log and the Event Viewer. **TraceToDeveloper** writes to the trace log only. Some variations allow you to specify a category and others don't. At this point, you really want to look at the many options and narrow down the list to one or two that are going to be most useful to you. I generally only use the following depending on whether I'm logging exceptions or information:

[csharp]
void LogToOperations(Exception exception, int eventId, EventSeverity severity, string category);
void LogToOperations(string message, string category);
[/csharp]

Notice the **category** parameter which is where we set both the area _and_ category to use. The supplied string here needs to be of the format "_Area_/_Category_". This is fairly distasteful in design and much like a "[magic number](http://en.wikipedia.org/wiki/Magic_number_(programming))", but that's the way the API has been written. I recommend creating a LoggingHelper class that abstracts this away and reduces the risk of making a mistake.

**Registering the areas and categories**

There is one last trick to perform before using these custom areas and categories. They must be provisioned as part of a farm level feature. This is primarily because updates are made to the SharePoint configuration database which requires execution by an account with farm level permissions. It's also because registering new sources for the Event Viewer to use must be done by a user with permissions to write to the Windows registry. Executing this registration as a farm-level event receiver should alleviate these issues (unless you're deploying to a locked down environment where you may have some problems).

There is sample code on how to register areas and categories in the [SharePoint Guidance documentation](http://msdn.microsoft.com/en-us/library/ff798462.aspx). (There's also plenty of other great stuff there about how the logger works and I recommend taking a few minutes to read it.) One extra thing to take note of if you plan on logging to the Event Viewer in a **multi-server farm** is that this registration needs to take place on each server in the farm. There are [instructions on how to do this](http://blogs.msdn.com/b/chriskeyser/archive/2010/12/20/using-a-sharepoint-timer-job-to-deploy-settings-in-a-farm-for-registering-event-sources.aspx) by lead SharePoint p&p; developer Chris Keyser.

**Segue - suggestions**

As you would have gathered, this library is not perfect. Here's some thoughts on gotchas and what I feel could be improved:




	
  * The ILogger interface contains too many method signatures which causes confusion. Most developers will only settle on their chosen one or two. I'd like to see a simple entity-style class used for specifying logging parameters instead of the many options given.

	
  * Using a 'magic number' style string format to select an area and category is error prone. Area and category should be separated, and preferably strongly typed.

	
  * It's clear to see the ILogger interface has been designed around logging to the ULS and Event Log. This makes it not as adaptable to other scenarios. Again, using an entity-style class for parameters would help make it more generic.

	
  * To register your own custom areas and categories, a farm feature is required so that enough permissions are available to write to the Windows registry. In multi-server farms there is added complexity.



On the plus side, a lot of these issues only need to be resolved once.

**Conclusion**

I really like the logging functionality provided with the patterns & practices library. It's generally easy to use and certainly powerful. There are some issues, but it's a great way to get exposure to the benefits of this library and immediately reap rewards!

