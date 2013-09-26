---
author: admin
comments: true
date: 2009-11-06 18:53:56+00:00
layout: post
slug: administrators-stop-before-deploying-that-custom-code
title: Administrators! Stop before deploying that custom code!
wordpress_id: 271
categories:
- SharePoint
tags:
- deployment
- development
- error
- memory
- SharePoint
---

Even with no development knowledge, there's a simple and quick tool that SharePoint administrators can run before deploying a solution package or web part to get some idea on whether it could cause instability your farm! It's [SPDisposeCheck](http://code.msdn.microsoft.com/SPDisposeCheck) and is particularly useful to keep an eye on the work done by a third party or even internally, with no developer knowledge whatsoever.

The tool was released by Microsoft in early 2009 to help developers check for memory leaks in their code. However there's no reason why an administrator can't do the same. Memory leaks can cause [performance problems and general instability](http://msdn.microsoft.com/en-us/library/aa973248.aspx) in your SharePoint farm. This tool tells you of any potential problems, and here's how to use it:




	
  1. Download [SPDisposeCheck](http://download.microsoft.com/download/B/4/D/B4D279A0-E159-40BF-A5E8-F49ABDBE95C7/SPDisposeCheck.msi) and install. By default this will copy SPDisposeCheck.exe to `C:\Program Files\Microsoft\SharePoint Dispose Check`.

	
  2. Examine the deployment file you have been given. If this is a WSP file, rename its extension to CAB and extract all DLL files. SPDisposeCheck also works on EXE files if you've been asked to run a console application.

	
  3. For each DLL or EXE, run SPDisposeCheck using the filename as a parameter. To be assured of no SharePoint memory leaks, check that the "Total Found" line is 0.



Here's an example that shows a potential leak:

[![SPDisposeCheckOutput]({{ BASE_PATH }}/images/SPDisposeCheckOutput.png)

If the tool finds some issues then you should query this with the developers. However as the tool itself states, this doesn't necessarily mean there is a leak! Unfortunately it can report false positives depending on how the code has been written. Regardless, make sure you have an assurance that the tool is run as part of their release process and any flagged issues have been checked. The tool will never miss a problem with a false negative.

It's as simple as that. Be sure to add SPDisposeCheck to your admin toolkit and don't let those developers get away with dodgy code!
