---
author: admin
comments: true
date: 2009-08-24 18:20:18+00:00
layout: post
slug: when-alerts-dont-work-by-design
title: When alerts don't work 'by design'
wordpress_id: 226
categories:
- SharePoint
tags:
- alerts
- bug
- SharePoint
---

I've just spent many fruitless hours trying to find a bug in my code that has been causing minor but visible pain for a number of users - and it was never even there. You would think after spending several years working with SharePoint I would know all of the ins-and-outs of alerts but not this one!

**Scenario**




	
  * "Require Check Out" is set to Yes (toggle the screenshot below in your mind, please!)  

[![Require Check Out]({{ BASE_PATH }}/images/requireco.png)

	
  * Alert created to only send when new items are added  

[![Send alerts when new items are added]({{ BASE_PATH }}/images/alertnew.png)



**Problem**

User does not receive alerts on new items.

**What's happening?**

When "Require Check Out" is enabled, alerts for a new document are only processed when it is checked in, and _only the most recent alert event is kept_. Here's a simplified version of what SharePoint is doing behind the scenes when a user sets an alert for new items only:







Action
Alert-related behaviour
Response to user





User uploads a document


"New item" event registered


"Fill in properties and check in" page displays








User clicks "Check In"


"Changed item" event registered and alert functionality triggered - most recent event is processed, sending a "Changed item" alert ("New item" event is discarded)


Document checked in and user receives no alert




This is also an issue for users opting to receive alerts with the "All changes" or "Existing items are modified" options set. Their alerts will read the item has changed when actually it is new.

I asked about this on the SharePoint forums and received a good [response from Lu Zou-MSFT](http://social.technet.microsoft.com/Forums/en-US/sharepointgeneral/thread/948a168d-22f3-4a09-92d7-2e6558b0f0fb) confirming this behaviour. Unfortunately it's unknown if this behaviour will change for SharePoint 2010.

