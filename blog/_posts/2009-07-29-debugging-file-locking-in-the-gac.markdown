---
author: admin
comments: true
date: 2009-07-29 20:25:23+00:00
layout: post
slug: debugging-file-locking-in-the-gac
title: Debugging file locking in the GAC
wordpress_id: 176
categories:
- SharePoint
tags:
- debugging
- deployment
- development
- SharePoint
---

Do you ever find that your solution files have been "successfully deployed" according to SharePoint, yet the updated code isn't taking effect? Perhaps you are trying to debug but breakpoints aren't being hit when normally there's no problem. Yet for some reason rebooting the server fixes the problem...

If that's the case, the reason is probably **file locking**, most likely to occur if you have assemblies that need to be deployed to the Global Assembly Cache (GAC). If you ever find yourself in this situation, download a copy of the excellent [Process Explorer](http://technet.microsoft.com/en-us/sysinternals/bb896653.aspx) tool, and follow these steps to verify:



	
  1. Start [Process Explorer](http://technet.microsoft.com/en-us/sysinternals/bb896653.aspx) on the server affected.

	
  2. Press Ctrl+F, or in the menu click **Find**, **Find Handle or DLL...**, or just click the binoculars icon on the toolbar:![Process Explorer toolbar]({{ BASE_PATH }}/images/petoolbar.png)

	
  3. Type the name of the assembly DLL and click **Search**.

	
  4. Under the Handle or DLL column, look for any assemblies located in the path **C:\WINDOWS\assembly\temp**. These lines tell you what process is locking the assembly.


[caption id="attachment_181" align="aligncenter" width="638" caption="OWSTIMER.EXE is the culprit!"]![OWSTIMER.EXE is the culprit]({{ BASE_PATH }}/images/peasmlist.png)[/caption]

The **C:\WINDOWS\assembly\temp** folder is Windows' equivalent of "assembly purgatory" where the assembly will sit until Windows can deploy it. If the currently deployed assembly cannot be overwritten as it is already locked by a process then the DLL will stay there until the process releases the lock or ends, or until Windows is restarted (also ending the process).

One example where this problem can occur is when a WSP solution containing custom e-mail event receivers is upgraded. The timer service, OWSTIMER.EXE, executes event receivers via the **Windows SharePoint Services Incoming E-Mail** job. As the OWSTIMER.EXE process isn't restarted when a solution is deployed, it will always hold onto the lock for the DLL containing the event receivers. The solution is to restart the **Windows SharePoint Services Timer** service on each web front end server. If you're finding this problem in development, [WSPBuilder](http://wspbuilder.codeplex.com) v1.06 adds a "Recycle the Windows SharePoint Services Timer" option.

This problem can also occur when using Visual Studio if certain combinations of add-ins are installed. You can again check this by using Process Explorer and verify if DEVENV.EXE is the locking process. If so, take a good look at the add-ins you actually need. Regularly restarting Visual Studio is very frustrating and will severely cut your productivity!
