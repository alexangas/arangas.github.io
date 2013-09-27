---
author: admin
comments: true
date: 2008-11-25 08:00:37+00:00
layout: post
slug: workspace-permissions-part-1-by-design
title: 'Workspace permissions - Part 1: "By Design"'
wordpress_id: 86
categories:
- Project Server
tags:
- permissions
- Project Server
- SharePoint
- workspaces
---

_A crucial foundation of SharePoint is its powerful permissioning model. This is a series of three posts that discusses SharePoint permission assignment in a Project Server project workspace. Part 1 explains how permissions are assigned and problems with the feature's design. Part 2 discusses a particular bug (i.e. the feature's implementation). Finally, Part 3 has some solutions._

Project Server has its own SharePoint security groups which almost precisely equate to the standard SharePoint groups of Read, Contribute, Design and Full Control. The TechNet article [Coordinate Project Server and Windows SharePoint Services security](http://technet.microsoft.com/en-us/library/cc197668.aspx) is the official word for exactly how permissions are assigned. Essentially just as SharePoint has its own built-in list of groups for "Read", "Contribute", "Design" and "Full Control", then Project Server comes to the party with "Readers (Microsoft Office Project Server)", "Team members (Microsoft Office Project Server)", "Project Managers (Microsoft Office Project Server)" and "Web Administrators (Microsoft Office Project Server)"... blurrgh!

Here is a simplified summary of the rules that explain how users get their permissions. This is without the "(Microsoft Office Project Server)" suffix but with standard SharePoint group names in brackets:



	
  * User that publishes the project has Project Manager (Design) permissions

	
  * Users assigned to tasks within the project have Team Member (Contribute) permissions

	
  * Users on the Resource Sheet of a project have Reader (Read) permissions

	
  * Users that have Manage Windows SharePoint Services permission have Web Administrator (Full Control) permission over all workspaces

	
  * Users that have Save Project permission have Project Manager (Design) permission over all workspaces



This looks complicated and it is. Try explaining to a project manager that access to documents in their workspace depends on who is added to the project's Resource Sheet. It just doesn't make sense! Then try explaining to an administrator why there is a group called "Readers (Microsoft Office Project Server)" as well as the standard SharePoint group called "Read". It just doesn't make sense!!

Then there's the inflexibility of the rules themselves. There are no options - they are either on or off. And if you don't go with MS on this one folks and uncheck the box for Workspace Permissions in Server Settings / Project Workspace Provisioning Settings, then only the Project Server service account is given access to new workspaces! This is hardly useful to the humble user needing to assign permissions who will never get their hands on _this_ particular username and password. It just doesn't make sense!

Don't worry, there are solutions. However, they don't come until Part 3! Next we look at how Microsoft's solution is also slow, and even worse, buggy.
