---
author: admin
comments: true
date: 2009-08-05 17:45:02+00:00
layout: post
slug: showing-the-bulleted-links-list-view
title: Showing the bulleted links list view
wordpress_id: 203
categories:
- SharePoint
tags:
- development
- SharePoint
- web part
---

A user recently took quite a shine to this lovely bulleted view of links in the list view web part:

[![Bulleted link list view]({{ BASE_PATH }}/images/usefullinks.png)

They wanted all of their existing links list to be changed over to use this view. What makes this case more unusual is that this is actually the view displayed as "<Summary view>" in the web UI:

[![Links toolpart]({{ BASE_PATH }}/images/usefultp.png)

After much fighting with the SPViewCollection on the list, I discovered [this post](http://ketulpatel.wordpress.com/2008/06/25/programatically-working-with-listviewwebpart-using-non-default-view/) on Ketul Patel's blog. He showed that to get a reference to this 'hidden' view, it is necessary to use the mouthy method [SPList.GetUncustomizedViewFromBaseViewId()](http://msdn.microsoft.com/en-us/library/microsoft.sharepoint.splist.getuncustomizedviewbybaseviewid.aspx). The list view web part can then be changed to use the markup for the summary view. (Note that the web part needs to be re-added to the page so that the changes take effect correctly.) Here is a method that does this all this:

[csharp]
public static ListViewWebPart ReplaceWithBaseView(
	ListViewWebPart listViewWebPart, SPList list,
	int baseViewId, SPLimitedWebPartManager limitedWebPartManager)
{
	string zoneID = listViewWebPart.ZoneID;
	int zoneIndex = listViewWebPart.ZoneIndex;

	SPView view = list.GetUncustomizedViewByBaseViewId(baseViewId);

	ListViewWebPart replacementWebPart = new ListViewWebPart
	{
		Title = listViewWebPart.Title,
		ListName = listViewWebPart.ListName,
		Hidden = false,
		FilterString = listViewWebPart.FilterString,
		SuppressWebPartChrome = listViewWebPart.SuppressWebPartChrome,
		ViewContentTypeId = listViewWebPart.ViewContentTypeId,
		ListViewXml = view.HtmlSchemaXml,
		ExportMode = listViewWebPart.ExportMode
	};

	limitedWebPartManager.DeleteWebPart(listViewWebPart);
	limitedWebPartManager.AddWebPart(replacementWebPart, zoneID, zoneIndex);

	return replacementWebPart;
}
[/csharp]

In order to call the above method, it's simply a matter of getting a reference to the web part page and the web part. The magic number for **"<Summary view>" is 0**. Here is the calling code:

[csharp]
SPList usefulLinksList = web.Lists["Useful Links"];
using (SPLimitedWebPartManager webPartManager =
		web.GetLimitedWebPartManager("default.aspx", PersonalizationScope.Shared))
{
	try
	{
		foreach (WebPart webPart in webPartManager.WebParts)
		{
			if (webPart.Title == "Useful Links")
			{
				ListViewWebPart listViewWebPart = (ListViewWebPart)webPart;
				ReplaceWithBaseView(listViewWebPart, usefulLinksList, 0, webPartManager);
				break;
			}
		}
	}
	finally
	{
		webPartManager.Web.Dispose();
	}
}
[/csharp]


