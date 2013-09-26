---
author: admin
comments: true
date: 2009-07-31 06:35:11+00:00
layout: post
slug: explorer-view-breaks-after-ie8-install-on-windows-2003
title: Explorer View breaks after IE8 install on Windows 2003
wordpress_id: 198
categories:
- SharePoint
tags:
- error
- Internet Explorer
- SharePoint
---

Since installing Internet Explorer 8 on my Windows Server 2003 development machine I've been unable to use Explorer View, with the error:


> Please wait while Explorer View is loaded. If Explorer View does not appear, your browser may not support it.


Searching around, the main reasons seem to be:



	
  1. SharePoint site behind SSL - no fix at time of writing

	
  2. IE security level changes - fixable by adjusting security levels or adding SharePoint site to Trusted Sites zone

	
  3. WebClient service not running - fixable by starting this Windows service and ensure it is set to start automatically


For me the problem was #3, however attempts to start WebClient resulted in "File not found"-type errors. The following error was logged in the Event Viewer:


> The application-specific permission settings do not grant Local Activation permission for the COM Server application with CLSID
{BA126AD1-2166-11D1-B1D0-00805FC1270E}
to the user NT AUTHORITY\NETWORK SERVICE SID (S-1-5-20).  This security permission can be modified using the Component Services administrative tool.


This familiar problem usually occursin SharePoint installations with the **IIS WAMREG admin Service** in Component Services. However this time searching the registry for the GUID in the error I had came up with the "Network Connection Manager Class". Then searching for the GUID in its AppID key of {27AF75ED-20D9-11D1-B1CE-00805FC1270E} resulted in the **netman **component.

I then configured the security as described by the error in Component Services:

[![netman Launch Permission](http://alexangas.com/blog/wp-content/uploads/2009/07/nslp-248x300.png)](http://alexangas.com/blog/wp-content/uploads/2009/07/nslp.png)

After stopping and restarting MS DTC I was able to start WebClient successfully and Explorer View worked again.
