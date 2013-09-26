---
author: admin
comments: true
date: 2009-07-28 14:13:05+00:00
layout: post
slug: gotchas-configuring-sso
title: Gotchas configuring SSO
wordpress_id: 191
categories:
- SharePoint
tags:
- SharePoint
- SSO
---

SharePoint 2007 seems to have some real weirdness going on with configuring SSO. I've been setting it up in a test environment in a very simple way, using the farm administrator account for everything: Microsoft Single Sign-on Service account, Single Sign-On Administrator Account, Enterprise Application Definition Administrator Account, etc... Note that _this is for testing only and is not best practice_! Here are the problems I've found...

**Weirdness #1:** Error 0x80630005. Verify this account has sufficient permissions and try again.

**Resolution:** Log in to the server as the "Microsoft Single Sign-on Service" service account. As described by [Frank Grossmann](http://cs.grossmann.com/blogs/fg/archive/2007/09/02/setting-up-single-sign-on-in-sharepoint-2007-errors.aspx).

**Weirdness #2:** Error 0x8063064a. Verify this account has sufficient permissions and try again.

**Resolution:** Remote desktop to the server's console (i.e. use **mstsc /console**) or physically log in to the server. As described by [Dave Sobel](http://msmvps.com/blogs/OBTS/archive/2005/08/20/63535.aspx). The [Microsoft documentation for configuring SSO](http://technet.microsoft.com/en-us/library/cc262932.aspx) implies this but is not explicit:


> Note that you must be logged into the SharePoint Central Administration Web site on a farm server to configure single sign-on (SSO) for Office SharePoint Server 2007. If you attempt to configure SSO on a workstation or any computer that is not a farm server, you will see an error message that reads "Single sign-on cannot be configured from this server. To configure single sign-on, go to the computer running the single sign-on service and specify these settings locally."


I haven't finished the configuration yet but hopefully there won't be more problems. The best guide I've found is [MOSS Single Sign On Setup Step-By-Step](http://www.thorprojects.com/blog/archive/2008/08/02/moss-single-sign-on-setup-step-by-step.aspx) and beats what Microsoft provides.
