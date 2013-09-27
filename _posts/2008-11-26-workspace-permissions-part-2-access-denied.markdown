---
author: admin
comments: true
date: 2008-11-26 08:00:48+00:00
layout: post
slug: workspace-permissions-part-2-access-denied
title: 'Workspace permissions - Part 2: "Access Denied"'
wordpress_id: 96
categories:
- Project Server
tags:
- permissions
- Project Server
- SharePoint
- workspaces
---

_A crucial foundation of SharePoint is its powerful permissioning model. This is a series of three posts that discusses SharePoint permission assignment in a Project Server project workspace. [Part 1](http://alexangas.wordpress.com/2008/11/25/workspace-permissions-part-1-by-design/) explained how permissions are assigned and problems with the feature’s design. Part 2 discusses a particular bug (i.e. the feature’s implementation). Finally, Part 3 has some solutions.
_

If you've been having some great successes out of your Project Server solution you'll probably be building up quite a few users and workspaces. One particular customer I'm aware of has over 1000 of each. To try and get around the inflexible rules outlined in [Part 1](http://alexangas.wordpress.com/2008/11/25/workspace-permissions-part-1-by-design/), they've decided to give every user at least read access to every workspace. Unfortunately this has uncovered a particularly nasty SQL deadlock bug.

`10/06/2008 15:33:59.86 Microsoft.Office.Project.Server (0x1540)   0x12C4 Windows SharePoint Services     Database                                  6f8g      Unexpected       Unexpected query execution failure, error code 1205. Additional error information from SQL Server is included below. "Transaction (Process ID 151) was deadlocked on lock resources with another process and has been chosen as the deadlock victim. Rerun the transaction." Query text (if available): "{?=call proc_SecAddPrincipalToRole(?,?,?,?,?,?)}"
`

The bug happens when one or more of the following operations is occurring at a time:



	
  1. Creating a new workspace by publishing a project (queue job "WSS Workspace Create")

	
  2. Resynchronising workspace permissions from Server Settings / Project Workspaces (queue job "WSS Workspace Membership Synchronisation")

	
  3. Changing a user's Project Server groups which results in changes to SharePoint permissions (queue job "User Synchronization for Project Web Access App Root Site and Project WSS Workspaces")



When the bug hits, permissions are only partially assigned so as this happens again and again, a user's consistency of access disappears. The only solution is to try and resynchronise the workspace with fingers crossed that it succeeds without another SQL deadlock. But you're completely out of luck when running a batch job that affects several workspaces such as number 3 above. The workspace it reached before everything went horribly wrong doesn't appear to be logged!

Because of the SQL deadlock failures, the [SynchronizeMembershipForPwaAppRootSite()](http://msdn.microsoft.com/en-us/library/websvcwssinterop.wssinterop.synchronizemembershipforpwaapprootsite.aspx) method in the [WssInterop](http://msdn.microsoft.com/en-us/library/websvcwssinterop.wssinterop_methods.aspx) web service has been looked at. This performs a complete resynchronisation across all workspaces, functionality that is not available through the UI. Unfortunately with testing in UAT (less powerful than production but still a three server farm), this operation was going to run for days. Also, no other sync jobs could occur at the same time or else it would be SQL deadlock again!

Bugs are the worst, but implementation is also a shocker because of the huge length of time these jobs take to run. For example, up to 7 minutes for numbers 1 or 2 above, and 15-45 minutes for number 3 depending on number of users with changed permissions. This is exacerbated as more workspaces are created (the problem seems to be more related to number of workspaces rather than number of users). The server environment where this issue is occurring is very powerful and speed should not be an issue.

We've seen from [Part 1](http://alexangas.wordpress.com/2008/11/25/workspace-permissions-part-1-by-design/) the deficiencies in design of this feature, but now also it's implementation. So things can only get better in Part 3, coming next!
