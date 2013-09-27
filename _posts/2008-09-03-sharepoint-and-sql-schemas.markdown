---
author: admin
comments: true
date: 2008-09-03 15:57:17+00:00
layout: post
slug: sharepoint-and-sql-schemas
title: SharePoint and SQL schemas
wordpress_id: 41
categories:
- Project Server
- SharePoint
tags:
- Project Server
- SharePoint
- SQL
- update
---

I'm just under two weeks away from installing the infrastructure update to a production environment. But of course these things need testing first so I started the install on the development environment. Thing's went hairy for a while...

First I must admit I'm not running the update as the service account, [recommended by Microsoft](http://technet.microsoft.com/en-us/library/cc671413.aspx). This is because I'm working in a locked down environment where trying to get the permission to do this requires a lot of questions, moaning and red tape. However both my account and the service account (there's only one service account in use here) are local administrators.

The log file pointed to by the SharePoint Configuration Wizard contains this ever so helpful line: "An exception of type Microsoft.SharePoint.PostSetupConfiguration.PostSetupConfigurationTaskException was thrown.  Additional exception information: Failed to upgrade SharePoint Products and Technologies." However, with a little more digging there are details located at C:\Program Files\Common Files\Microsoft Shared\web server extensions\12\LOGS. (This isn't actually the folder where I have directed diagnostic logs to, but for the purposes of an upgrade it seems SharePoint sometimes redirects it!) The logs contain errors over the several times I tried to upgrade...

Upgrade.log:



	
  * SqlException: The default schema does not exist.

	
  * SqlException: Cannot find the object 'MFN_Epm_GetAllCustomFieldsInformation', because it does not exist or you do not have permission.

	
  * SqlException: Cannot drop the function 'dbo.MFN_Epm_GetAllCustomFieldsInformation', because it does not exist or you do not have permission.



Then in the ULS logs:

	
  * The schema version (3.1.4.0) of the database SharePoint_AdminContent on server_x is not consistent with the expected database schema version (3.1.5.0) on server_x.  Connections to this database from this server have been blocked to avoid data loss.  Upgrade the web front end or the content database to ensure that these versions match.

	
  * SqlException: Cannot find the user 'DOMAIN\myusername', because it does not exist or you do not have permission.

	
  * Queue unable to interact with SQL. Queue type (Project Queue, Timesheet Queue etc): ProjectQ Exception: Microsoft.Office.Project.Server.BusinessLayer.Queue.QueueSqlException: PeriodicTasks failed ---> System.Data.SqlClient.SqlException: Invalid object name 'MSP_QUEUE_RENDEZVOUS'.



It appears that I don't have permission to the databases. However, my account is db_owner on every database The service account permissions haven't been changed. What's going on?!! It turns out the root of the cause was described this exception: **The default schema does not exist**.

I don't know a great deal about the schema feature introduced with SQL 2005, however with every user added to a SQL 2005 box, an accompanying schema with the same name is also created. A colleague of mine who was doing some spring cleaning on our databases removed the schema for our service account, however the service account's SQL user was still trying to use it as its default schema! The solution was to change the schema to **dbo**.

Unfortunately the upgrade still didn't quite complete since I was part of the way through the upgrade when it fell over... I also had to create a fake MFN_Epm_GetAllCustomFieldsInformation function in the Reporting database so the upgrade could find and drop it (fortunately replaced with the new version in the upgrade).

It was a big relief that this ended up working. The downside is that my user account and a schema for it have been created on the database server. It can't be removed right now because the new database objects that come with the update have my schema set as their owner! Now I have to change them all...
