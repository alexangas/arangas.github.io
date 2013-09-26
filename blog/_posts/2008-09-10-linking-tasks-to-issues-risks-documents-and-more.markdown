---
author: admin
comments: true
date: 2008-09-10 14:14:55+00:00
layout: post
slug: linking-tasks-to-issues-risks-documents-and-more
title: Linking tasks to issues, risks, documents, and more...
wordpress_id: 43
categories:
- Project Server
tags:
- feature
- Project Server
---

In Project Server 2003 a feature was introduced to link issues, risks and documents to tasks. It's been carried over to 2007 as well (although the UI for it isn't as noticeable) and I think it's one of the coolest yet underrated features in the web interface. Let me take you through it... (Note: you will need the Create Object Link category permission to use this feature.)

Every project workspace out of the box comes with the Project Documents, Risks and Issues lists. On the add and edit pages of these lists, you can find "Link Items":
![](http://alexangas.files.wordpress.com/2008/09/link-items-link.png)

Clicking Link Items while creating or editing an issue, for example, allows the issue to be linked to any task in the project. Also, this linking can have several meanings, such as whether the issue simply relates to the task, affects the task, or whether the task resolves the issue. Here is the dialog:
![](http://alexangas.files.wordpress.com/2008/09/link-task-to-issue.png)

You'll notice the drop-down at the top that reads Project Tasks. This can be changed so that this issue is linked to any risk, document or other issue as well! (Note there is no additional 'meaning' unless linking to a task.)

Upon clicking OK on this dialog box, a nice little summary is displayed of the link options chosen:
![](http://alexangas.files.wordpress.com/2008/09/link-task-to-issue-summary.png)

Of course, the issues list is just an example here. It is also possible to link items from the other lists previously mentioned. Here are the other dialog boxes shown...

Risk:
![](http://alexangas.files.wordpress.com/2008/09/link-task-to-risk.png)

Document:
![](http://alexangas.files.wordpress.com/2008/09/link-task-to-document.png)

_Linking's cool, but now what?_

Obviously whenever you look at a linked object you will be able to see and click on what each object is linked to which helps to describe the relations within your project, not to mention handy as well. Some examples are linking a document to a task for its delivery, or linking a risk item to a risky task! Also very useful is that the link gets propagated to the reporting database when the workspace's project is next published, so some neat queries can be written to relate your data together.

What's even better is what you don't see. This functionality is powered by the Object Link Provider, which is described in some detail within the [Project 2007 SDK](http://msdn.microsoft.com/en-us/library/aa568840.aspx). With some custom development work, it is possible to link tasks or project workspace items in other lists to any object you can reach with a URL. Imagine the possibilities - linking to an item in the company's financial system, for example! This can all be done with the ObjectLinkProvider web service (examine the SDK for more details).

_A few cautionary words_

As you may have already encountered, some functionality in Project Server can break when you start altering project workspaces. This is even though it does nothing to stop you and virtually every customer/user has a requirement to. The Link Items feature is no exception here. If you delete the Links column from a workspace, there is no way I've found to put it back (this is probably because it is not a built-in SharePoint field type). Instead, try setting it to Hidden within the content type! In fact, hiding the field in the list's content type should be done for every workspace field you don't want to use.

_Thanks for reading_

Hopefully this provides a deeper look at a feature that doesn't seem to get mentioned much. If you have good uses for "Link Items", please add a few comments to this post!
