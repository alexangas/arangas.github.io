---
author: admin
comments: true
date: 2008-10-14 14:51:37+00:00
layout: post
slug: programmatically-retrieving-the-name-of-an-ssp
title: Programmatically retrieving the name of an SSP
wordpress_id: 79
categories:
- Project Server
- SharePoint
tags:
- development
- Project Server
- PSI
- SharePoint
- SSP
---

This [was asked](http://stackoverflow.com/questions/58809/retrieving-the-associated-shared-service-providers-name) on the new Stack Overflow programming questions site recently (which you should really check out and start using if you don't already!). Getting the SSP name is something that I've needed to do to build the SSP URL for [impersonating access to the Project Server PSI](http://msdn.microsoft.com/en-us/library/aa974413.aspx). I used to think that the URL had to live in a config file. Then after some digging into Project Server's behaviour with the help of [Reflector](http://www.red-gate.com/products/reflector/), I discovered Microsoft use the SharedResourceProvider class in the Microsoft.Office.Server.Administration namespace. Who knows why, but this class is marked internal so reflection is required to access it, and therefore accessing it is not supported. We should be on relatively safe ground if MS products such as Project Server use it however...

The following code retrieves the name of the SSP for the current site collection.
[sourcecode language='csharp']
SPSite site = SPContext.Current.Site;
ServerContext serverContext = ServerContext.GetContext(site);

PropertyInfo srpProp = serverContext.GetType().GetProperty(
	"SharedResourceProvider",
	BindingFlags.NonPublic | BindingFlags.Instance);
object sharedResourceProvider = srpProp.GetValue(serverContext, null);
PropertyInfo srpName = sharedResourceProvider.GetType().GetProperty(
	"Name",
	BindingFlags.Public | BindingFlags.Instance);

string sspName = (string)srpName.GetValue(sharedResourceProvider, null);
[/sourcecode]

From there the URL to the PSI web services within the SSP directly becomes the following (and the port number is always the following on every server):
http://_servername_:56737/_SSPName_/PSI/ or
https://_servername_:56738/_SSPName_/PSI/

This does seem overly complex so if someone out there has a better solution please add it as a comment to this blog or update the [answer on Stack Overflow](http://stackoverflow.com/questions/58809/retrieving-the-associated-shared-service-providers-name).
