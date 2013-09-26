---
author: admin
comments: true
date: 2011-09-13 12:10:56+00:00
layout: post
slug: analysis-of-the-oracle-hyperion-web-part
title: Analysis of the Oracle Hyperion Web Part
wordpress_id: 168
categories:
- SharePoint
tags:
- deployment
- Oracle
- SharePoint
- web part
---

This is a post I originally wrote in July 2009 and never got around to properly completing. Because of its age I considered binning it altogether however a quick web search seems to turn up that nothing has changed for this humble web part. This is more of a rant about vendors that can't be bothered to integrate their products with SharePoint using best practices. The person that ends up suffering is "the SharePoint guy/gal" who is lumped with deploying this rubbish. Hopefully it helps someone else out there that's stuck in the same boat I once was.



# Rewind to July 2009



I was recently given the task of deploying the Oracle Hyperion web part for testing in one of our SharePoint sites. There was no formal requirement for this, it was simply requested because there appears to be no documentation or information available on what functionality this mysterious web part can provide. What better way to find out than to deploy it for testing, right? There are even quite [detailed instructions](http://download.oracle.com/docs/cd/E12825_01/epm.111/pit_setup.pdf) available!

Well stop right there, folks. In fact before you read any further, my recommendation is to run far, far away from this unusual piece of technology. Why? Well let's start with the high-level changes you'll need to make to your SharePoint environment:



	
  * Add the web part itself

	
  * Configure single sign-on (SSO) - makes sense as Oracle is an external product

	
  * Create a 'proxy page' - a virtual IIS application inside your SharePoint application

	
  * Install a custom HTTP handler

	
  * Create a custom database


Hmmm... seems rather unusual so far (particularly that custom database - don't we already have data stores in both SharePoint **and** Oracle?). But then read the fine print as it describes three best practice no-nos all on one page (that's page 71 if you're following along readers) without even so much as to explain what effect the changes will have.



	
  1. **Set the trust level to Full.**
Say goodbye to security in your SharePoint farm right here. With trust level set to Full, [custom code can run without restriction](http://blog.tylerholmes.com/2008/10/don-set-your-sharepoint-app-to-full.html) and do whatever it likes. **Really Bad Idea.** Oracle, please look into Code Access Security.

	
  2. **Replace the default SQL Server session state provider for SharePoint.**
I'll be honest. I have no idea what they are doing here or why. All I know is that the chances of support from Microsoft on such a fundamental change to how session state works in SharePoint has to be zero.

	
  3. **Comment out the PublishingHttpModule.**
Say goodbye to one of the best features in SharePoint - publishing pages. For yet another strange and unexplained reason, this has to go. Not sure if they put it back somewhere with their custom code, this isn't mentioned.


Now follow through almost twenty pages, some with screenshots, to configure this nightmare, and that doesn't included setting up SSO. I like to think I'm pretty good at puzzling out this sort of thing, but this baby has got me beat.

Things I found that might help others... Add the server hosting SharePoint to the Local Intranet zone in Internet Explorer security settings. Otherwise, you might encounter an "Invalid index" message while browsing the repository in Internet Explorer. Also, uninstall the Enhanced Security Configuration from Windows Server to prevent logon issues.

I wonder if anyone, anywhere has been able to successfully deploy this thing? More to the point, why did Oracle bother to even develop it?
