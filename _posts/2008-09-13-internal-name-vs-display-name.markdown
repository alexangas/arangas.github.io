---
author: admin
comments: true
date: 2008-09-13 11:37:39+00:00
layout: post
slug: internal-name-vs-display-name
title: Internal name vs display name
wordpress_id: 72
categories:
- SharePoint
tags:
- development
- SharePoint
---

Anyone that's done a reasonable amount of work with the SharePoint object model will soon come across its flaws. The one that I regularly come up against is when working with the SPField object that represents list columns.

There are inconsistencies throughout the object model when attempting to retrieve an instance of an SPField, its value, or perform various other operations. In some cases the display name is required, or the internal name, or both! Even worse, rarely does the SDK document what is required to find your field. Unpredictable behaviour can also result in the cases where it checks for display name and internal name, because what if you have two fields with the same display name?!

Here's just a few frustrating examples:



	
  * **SPFieldCollection.ContainsField:** Internal name _or_ display name

	
  * **SPFieldCollection.Item[string]:** Display name

	
  * **SPViewFieldCollection.Exists:** Internal name



Tom, another SharePoint developer, has made [a list with more examples here](http://tomblog.insomniacminds.com/2008/01/25/sharepoint-internals-internalname-versus-displayname/). He also details the inconsistencies around when an exception is thrown - yet another place to get caught out.

One of the most important things in an API is consistency so it behaves like you would expect. I'm sure how Microsoft can fix this without introducing breaking changes so maybe we will have to live with this for a long time yet. If in doubt, as always, [Reflector](http://www.red-gate.com/products/reflector/) is your friend.
