---
author: admin
comments: true
date: 2008-11-27 08:00:26+00:00
layout: post
slug: workspace-permissions-part-3-workarounds-and-the-future
title: 'Workspace permissions - Part 3: "Workarounds and The Future"'
wordpress_id: 100
categories:
- Project Server
tags:
- permissions
- Project Server
- SharePoint
- workspaces
---

_A crucial foundation of SharePoint is its powerful permissioning model. This is a series of three posts that discusses SharePoint permission assignment in a Project Server project workspace. [Part 1](http://alexangas.wordpress.com/2008/11/25/workspace-permissions-part-1-by-design/) explained how permissions are assigned and problems with the feature’s design. [Part 2](http://alexangas.wordpress.com/2008/11/26/workspace-permissions-part-2-access-denied/) discusses a particular bug (i.e. the feature’s implementation). Finally, Part 3 has some solutions._

We've looked at the problems with this feature, so what can we do about it? Well the new Project Server 2007 eventing model isn't broken, so if you have a developer handy use that!

First, uncheck the Workspace Permissions box in Server Settings / Project Workspace Provisioning Settings. Then, have a look at the WssInterop event WssWorkspaceCreated. This fires whenever, um, a workspace is created. The initial state of a created workspace is with permission inheritance broken and the service account assigned to the workspace with Full Control.

From here, use [SPWeb.EnsureUser](http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.spweb.ensureuser.aspx) for each user you'd like to give access to the workspace. This works by adding the user to the SPWeb if they haven't been already. This does not give the added users any permissions to the workspace. SPWeb.EnsureUser also works with Active Directory groups, so a model where every user needs read access can be simply accomplished. Finally, use [SPWeb.RoleAssignments](http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.spweb.roleassignments.aspx), [SPWeb.RoleDefinitions](http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.spweb.roledefinitions.aspx), and [SPRoleAssignment.RoleDefinitionBindings](http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.sproleassignment.roledefinitionbindings.aspx) to assign users the correct permissions (sorry, hopelessly glossed over but there are examples out there on how to use these).

Something else to note about a custom solution is that despite unchecking the Workspace Permissions box earlier on, Project Server's behaviour does not change when giving access to the PWA site. It will still handle this for you according to the rules in [Part 1](http://alexangas.wordpress.com/2008/11/25/workspace-permissions-part-1-by-design/), leaving you to configure the permissions for the workspace with custom code.

This works fine and if you are able to make use of Active Directory groups, is fast as well. Plus now that you've gone custom, you have all of the freedom (and traps) of flexibility and the ability to create your own permission assignment rules. Don't forget you'll need to invest time in creating custom administration pages to manage those as well.

So what about going forward and sticking with the product for a solution? For Project Server 14 or 2009 or 2010 I have no idea if there will be any changes to the workspace permissions functionality. If you agree that this feature needs looking at, please give feedback to the [Project Server team](http://blogs.msdn.com/project/) as they do read it and listen (I've had great responses from [Treb Gatte](http://evildoctorporkchop.spaces.live.com/) and [Brian Smith](http://blogs.msdn.com/brismith/) on other issues). Hopefully bugs will be fixed and the feature improved next release!

Thanks for reading and I hope this helps someone else out there with similar experiences.
