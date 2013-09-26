---
author: admin
comments: true
date: 2008-09-30 09:21:49+00:00
layout: post
slug: sql-querying-the-queue
title: SQL querying the queue
wordpress_id: 77
categories:
- Project Server
tags:
- Project Server
- queue
- SQL
---

I recently found myself in the situation where the Project Server queue service was taking up almost all of a server's CPU. Unfortunately due to the high demands of the queue jobs, the administration page was unavailable with a connection timeout error.

One way to try and access the administration page again might have been to stop or restart the queue service or the entire server itself, but what if unpredictable behaviour had disrupted those jobs? Instead, by carefully looking at the Project Server databases we can see the draft project database stores a lot of information about the queue in tables and views starting with MSP_QUEUE.

The following query gives a summary of jobs in the queue:
[sourcecode language='sql']
SELECT
  QPG.GRP_QUEUE_ID,
  QPG.CREATED_DATE,
  QPG.READY_FOR_PROCESSING_DATE,
  QPM.MSG_QUEUE_BODY_TYPE,
  QPG.GRP_QUEUE_MESSAGE_TYPE,
  QPG.GRP_QUEUE_STATE,
  QPG.PROCESSING_DATE,
  QPG.PERCENT_COMPLETE,
  QPG.COMPLETED_DATE,
  QPG.GRP_QUEUE_ERROR_INFO,
  QPG.GRP_QUEUE_PRIORITY,
  QPG.LAST_MSG_ID,
  QPG.LAST_ADMIN_ACTION
FROM dbo.MSP_QUEUE_PROJECT_GROUP_FULL_VIEW QPG (NOLOCK)
INNER JOIN dbo.MSP_QUEUE_PROJECT_MESSAGE QPM (NOLOCK)
  ON QPM.GRP_UID = QPG.GRP_UID
ORDER BY GRP_QUEUE_STATE DESC, GRP_QUEUE_PRIORITY, QPG.GRP_QUEUE_ID
[/sourcecode]

This query gives how many jobs are remaining to run:
[sourcecode language='sql']
SELECT COUNT(*)
FROM dbo.MSP_QUEUE_PROJECT_GROUP_FULL_VIEW QPG (NOLOCK)
WHERE QPG.PROCESSING_DATE IS NULL
[/sourcecode]

This query gives how many jobs of each priority are remaining to run:
[sourcecode language='sql']
SELECT QPG.GRP_QUEUE_PRIORITY, COUNT(*) AS [COUNT]
FROM dbo.MSP_QUEUE_PROJECT_GROUP_FULL_VIEW QPG (NOLOCK)
WHERE QPG.PROCESSING_DATE IS NULL
GROUP BY QPG.GRP_QUEUE_PRIORITY
[/sourcecode]

There are also an interesting statistics table, although I haven't yet found how to get a text value for GRP_QUEUE_MESSAGE_TYPE:
[sourcecode language='sql']
SELECT QPS.*
FROM dbo.MSP_QUEUE_PROJECT_STATS QPS (NOLOCK)
[/sourcecode]

Ideally you would never want to execute raw SQL statements against the database, but as with any computer system, unpredictable things can happen. These queries might help with some troubleshooting.
