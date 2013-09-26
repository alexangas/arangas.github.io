---
author: admin
comments: true
date: 2008-07-28 19:00:22+00:00
layout: post
slug: project-server-itemswapper-control
title: Project Server ItemSwapper control
wordpress_id: 24
categories:
- Project Server
tags:
- control
- development
- Project Server
---

_ItemSwapper hasn't been documented by Microsoft so its behaviour may change between service packs - use at your own risk!_

With the 2007 version, Project Server is now a SharePoint application. One of the best things about this is the nifty stuff you get out of the box as part of SP. Custom controls are a highlight, of which there are many that can be used in your custom Project Server solutions. Even better is that Project Server 2007 comes with some cool controls of its own which you can use in any solution running within Project Server. My latest discovery is the handy **ItemSwapper**.

[caption id="attachment_29" align="aligncenter" width="510" caption="ItemSwapper"][![ItemSwapper](http://alexangas.files.wordpress.com/2008/07/itemswapper-intro.png)](http://alexangas.files.wordpress.com/2008/07/itemswapper-intro.png)[/caption]

This might look familiar to you from popular administration pages such as "Manage Queue" or "Manage Groups". Just by popping one of these on your page, you get a pretty flawless interface to selecting projects. It's also speedy and has some great features both client and server side. It's great to use in a web part used for selecting report parameters, so that's what we're going to build in this post!

First of all, let's have a look at two other custom controls used by ItemSwapper. There is the ItemSwapperLabel (extended from System.Web.UI.WebControls.Label) which renders the text above each list, and ItemSwapperSelect (extended from System.Web.UI.HtmlControls.HtmlSelect) which renders the list itself. Data binding is done with ItemSwapperSelect and the 'parent' ItemSwapper control renders all of these controls for you.

The ItemSwapper control refers to the two select lists as the 'alpha' and 'beta' lists (originally designed for Greek Project Server?). In our example the beta list will be blank for us to add projects into, so we only want to initalise the alpha list. Looking at the code, our CreateChildControls method contains:
[sourcecode language='csharp']protected override void CreateChildControls()
{
    alphaSelect = new ItemSwapperSelect();
    alphaSelect.Multiple = true;

    projectSwapper = new ItemSwapper();
    projectSwapper.ID = "projectSwapper";
    projectSwapper.TrackBetaListChanges = true;
    projectSwapper.AlphaSelect = alphaSelect;
    projectSwapper.LblAlphaList.Text = "Available Projects";
    projectSwapper.LblBetaList.Text = "Selected Projects";
    projectSwapper.DisplaySelectedItemText = false;
    projectSwapper.ShowBetaUpDownButtons = true;
    projectSwapper.ShowRestoreAllButton = true;
    projectSwapper.ShowRemoveAllButton = true;
    this.Controls.Add(projectSwapper);
}[/sourcecode]

This should be pretty self-explanatory (we will discuss the ID and TrackBetaListChanges properties later in this post). Now let's perform a data bind in our OnLoad method:
[sourcecode language='csharp']protected override void OnLoad(EventArgs e)
{
    if (!Page.IsPostBack)
    {
        DataTable projects = DataLayer.GetProjects();
        projectSwapper.DataSource = projects;
        projectSwapper.DataTextField = "Name";
        projectSwapper.DataValueField = "Uid";
    }
}[/sourcecode]

This will now render fine, but the finishing touch is a workaround to what I would call a bug. If you set ShowRestoreAllButton to true, this will display the "Add All" button. However it is important to know that this button is greyed out unless the user has selected an item first, meaning that will need two clicks to add all projects to the other list. I don't know why Microsoft would have done this, but to get around it, add the following code to our OnPreRender method:
[sourcecode language='csharp']protected override void OnPreRender(EventArgs e)
{
    alphaSelect.SelectedIndex = 0;
}[/sourcecode]
Have a look at the work so far as the ItemSwapper should now render!

Now let's make use of the items chosen in the lists. First of all we'll need a button in our web part for users to click on and start the report generation. We'll also need at least one hidden field that will contain the selected items for us to use on the server side. Note that there is quite a bit of data we can get from the ItemSwapper JavaScript object, such as a count of items for both lists, text labels of chosen items for both lists, and values (e.g. project GUIDs) of chosen items for both lists!

To be able to do retrieve the data on the client side there are two key properties we need to set when creating the ItemSwapper which I touched upon earlier: ID and TrackBetaListChanges (or if you care about the alpha list, TrackAlphaListChanges). We need to use JavaScript to get a reference to the ItemSwapper object and setting ID lets us use document.getElementByID() to do this. (Surprisingly and fortunately, ID doesn't get mangled in usual SharePoint fashion when rendered.) Setting TrackBetaListChanges sets a property on the JavaScript object to, well, track beta list changes.

So going back to CreateChildControls(), let's add the following code and connect our server code to the client:
[sourcecode language='csharp']chosenProjectValues = new HiddenField();
this.Controls.Add(chosenProjectValues);

createButton = new Button();
createButton.Text = "Create";
createButton.Click += CreateButton_Click;  // server-side processing done in this method
createButton.OnClientClick = String.Format("SaveItemSwapperValues('{0}', '{1}');", projectSwapper.ID, chosenProjectValues.ClientID);
this.Controls.Add(createButton);[/sourcecode]

Everything's ready now on the server side. We just need to create the SaveItemSwapperValues JavaScript function. Put this JavaScript wherever is standard for your projects.
[sourcecode language='jscript']function SaveItemSwapperValues(itemSwapperClientID, chosenValuesClientID)
{
    var chosenValuesField = document.getElementById(chosenValuesClientID);
    var itemSwapper = document.getElementById(itemSwapperClientID);
    chosenValuesField.value = itemSwapper.GetBetaListValuesAsString();
}[/sourcecode]

That's it! Now when your server-code hits CreateButton_Click(), you should find a nice comma-separated list of project GUIDs in the Value property of your hidden field that can be used to generate reports!

But it's nice to take things a bit further... Our Create button is always active, even when no projects are in the chosen list. Well then, behold! The ItemSwapper contains JavaScript event handling functionality! Add script that runs on startup such as this:
[sourcecode language='jscript']var itemSwapper = document.getElementById('projectSwapper'); // 'projectSwapper' should be passed as a parameter and not hard-coded
itemSwapper.OnAlphaListChange = ChosenProjectsChanged;
itemSwapper.OnBetaListChange = ChosenProjectsChanged;[/sourcecode]

Then add the following with the SaveItemSwapperValues function:
[sourcecode language='jscript']function ChosenProjectsChanged()
{
    var itemSwapper = document.getElementById('projectSwapper');
    var createButton = /* how you get this reference depends on your environment */
    if (itemSwapper.betaListItemCount == 0)
    {
        createButton.disabled = true;
    }
    else
    {
        createButton.disabled = false;
    }
}[/sourcecode]

Now we're talkin' - a smart ItemSwapper. And that's not all, there is heaps of handy JavaScript goodness you can hook on to if you're able to navigate to this file and have a look: C:\Program Files\Common Files\Microsoft Shared\web server extensions\12\TEMPLATE\LAYOUTS\PWA\LIBRARY\ItemSwapper.htc.

Last but not least, none of the ItemSwapper controls are sealed so you are free to extend them with your own functionality!
