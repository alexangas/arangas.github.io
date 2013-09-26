---
author: admin
comments: true
date: 2009-06-20 15:01:29+00:00
layout: post
slug: populate-a-sharepoint-site-collection-from-excel
title: Populate a SharePoint site collection from Excel
wordpress_id: 153
categories:
- SharePoint
tags:
- CodePlex
- excel
- SharePoint
- topology
---

I've needed to populate a site collection with many hundreds of sites in an automated fashion several times over the last couple of years. Every time the business owner gives me an Excel spreadsheet of the sites they want created. So rather than reinvent the wheel - again! - I've decided to start developing something reasonably generic and release it to my very first Codeplex project.

This first version is designed for a topology where each segment of the URL has its siblings in common. For example, **Industry > Brand > Product** or **Division > Sector > Department**. Depending on feedback I might make this more flexible.

Speaking of feedback, I'd really appreciate any comments from end users or other developers on desired features or comments on code WTFs! Please go to **[SPExcelator on Codeplex](http://spexcelator.codeplex.com/)**, download and add to the discussion!

Finally, special thanks to [Robin Meuré](https://twitter.com/robinmeure/status/2190226258) for suggesting use of the excellent [Excel Data Reader](http://exceldatareader.codeplex.com/) project.
