---
author: admin
comments: true
date: 2009-07-21 16:05:22+00:00
layout: post
slug: the-rows-you-modified-could-not-be-saved-to-the-server-due-to-validation-errors
title: '"The rows you modified could not be saved to the server due to validation
  errors"'
wordpress_id: 155
categories:
- SharePoint
tags:
- error
- SharePoint
---

Encountered a strange problem copying and pasting between the datasheet views of two contact lists. Only the Contact and Country fields were showing in the datasheet view.


When doing the paste, the following error appeared:




[caption id="attachment_159" align="aligncenter" width="690" caption="The rows you modified could not be saved to the server due to validation errors. This is usually caused by invalid default values."]![invalid1](http://alexangas.com/blog/wp-content/uploads/2009/07/invalid1.png)[/caption]

The error gives some clue as to the problem. I then attempted to add a single entry manually (without the paste) and received a new error:

[caption id="attachment_160" align="aligncenter" width="786" caption="Columns that require data are not included in this view. If you want to edit rows that are missing data in these columns, or add new rows, you must first add the required columns to the view."]![Columns that require data are not included in this view. If you want to edit rows that are missing data in these columns, or add new rows, you must first add the required columns to the view.](http://alexangas.com/blog/wp-content/uploads/2009/07/invalid2.png)[/caption]

Finally, I added all of the columns to the view and at last found the column causing the problem:

[caption id="attachment_161" align="aligncenter" width="288" caption="You must specify a non-blank value for Title."]![You must specify a non-blank value for Title.](http://alexangas.com/blog/wp-content/uploads/2009/07/invalid3.png)[/caption]

Setting the Title field to not be required fixed the issue!
