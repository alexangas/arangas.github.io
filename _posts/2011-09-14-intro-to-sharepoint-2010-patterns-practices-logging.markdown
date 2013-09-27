---
author: admin
comments: true
date: 2011-09-14 20:06:49+00:00
layout: post
slug: intro-to-sharepoint-2010-patterns-practices-logging
title: Intro to SharePoint 2010 patterns & practices - Logging
wordpress_id: 289
categories:
- SharePoint
tags:
- logging
- patterns-and-practices
- SharePoint
---

Microsoft's patterns & practices library for SharePoint 2010 contains plenty of useful information and tools. One of the simplest things you can start using right now is its logging functionality. Check out these handy features:




	
  * Log to both the ULS trace logs and the Windows Event Viewer

	
  * Define your own logging categories so they appear under **your** application's name everywhere

	
  * Support for logging from the sandbox via a provided proxy

	
  * Configure log event throttling using the out-of-the-box Diagnostic Logging page in Central Administration

	
  * Contextual information such as current URL and logged in user is automatically added to log entries

	
  * Don't worry about logging long messages or correlation IDs, it's all handled for you

	
  * Use the provided logging interface to easily write your own logger

	
  * Service locator provided means almost no code changes when changing to a different logger (for a future blog post)



If you're a developer that's never used the library before, this post helps you dip your toes in the water and harness this logging power!

**Getting started**

The p&p release is a little confusing. There's the [CodePlex project](http://spg.codeplex.com/), which links to various components and where you should ask for help or log any bugs you find. However the code is not kept there - download that from [MS Downloads](http://www.microsoft.com/downloads/details.aspx?FamilyID=64b55569-2168-4545-8b7c-f185b2cf967d). You'll want to keep an eye on this page for updates as they aren't announced anywhere that I've found.

Once extracted to your development server, you need to build the library. Everything is open source apart from the service locator (for a later blog post). The solution to build can be found at `Source\SharePoint 2010\Microsoft.Practices.SharePoint.sln`.

[![]({{ BASE_PATH }}/images/pandp_solution.png)

Build this project, then copy the output `Microsoft.Practices.ServiceLocation.dll` and `Microsoft.Practices.SharePoint.Common.dll` to your project and reference them.

**Log away!**

Using the logger is pretty easy. First, add these using statements everywhere you wish to log:

[csharp]
using Microsoft.Practices.SharePoint.Common.Logging;
using Microsoft.Practices.SharePoint.Common.ServiceLocation;
[/csharp]

Then declare the logger as a field and obtain a reference to it:

[csharp]
ILogger _logger = SharePointServiceLocator.GetCurrent().GetInstance<ILogger>();
[/csharp]

Now let's log an exception...

[csharp]
Exception ex = new ApplicationException("This is my test exception");
_logger.LogToOperations(ex);
[/csharp]

...and see what appears in the ULS log...



> 
09/08/2010 20:15:34.66 	w3wp.exe (0x15D8)                       	0x0EF0	Patterns and Practices        	SharePoint Guidance           	0000	Information	Category: SharePoint Guidance: An exception has occurred.   ExceptionType: 'ApplicationException'   ExceptionMessage: 'This is my test exception'   StackTrace: ''   Source: ''   TargetSite: ''   Additional Information:  Request TimeStamp: '2010-09-08T20:15:33.2171690+09:30'  UserName: 'AA\Administrator'  Request URL: 'http://aasp2010dev:31000/default.aspx?PageView=Shared&InitialTabId;=Ribbon.WebPartPage&VisibilityContext;=WSSWebPartPage'  User Agent: 'Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET4.0C; .NET4.0E; InfoPath.3; .NET CLR 3.5.30729; .NET CLR 3.0.30729)'  Originating IP address: '127.0.0.1'	2cc97a55-1c4d-4e22-b145-4de9a3fdf6e1




...and Event Viewer:

`
Log Name:      Application
Source:        Patterns and Practices
Date:          8/09/2010 8:15:34 PM
Event ID:      0
Task Category: None
Level:         Information
Keywords:      Classic
User:          N/A
Computer:      aasp2010dev.aa.com
Description:
Category: SharePoint Guidance: An exception has occurred.
	ExceptionType: 'ApplicationException'
	ExceptionMessage: 'This is my test exception'
	StackTrace: ''
	Source: ''
	TargetSite: ''

_(many other details removed for brevity)_
`

Here you can see the benefits these simple steps have already given - lots of context relating to exception to help diagnose the problem!

**Logging - make it happen!**

That's it for now, but we'll be exploring a lot more about this library in the next blog post. Remember - you owe it to yourself and your users to log appropriately. Ease the pain of bugs found in production code!

**Download and references:**



	
  * [SharePoint Guidance CodePlex Project](http://spg.codeplex.com/)

	
  * [SharePoint Guidance 2010 Download](http://www.microsoft.com/downloads/details.aspx?FamilyID=64b55569-2168-4545-8b7c-f185b2cf967d)



