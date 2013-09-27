---
author: admin
comments: true
date: 2011-10-31 19:05:19+00:00
layout: post
slug: do-your-research-before-developing-with-infopath
title: Do your research before developing with InfoPath
wordpress_id: 331
categories:
- InfoPath
tags:
- best-practices
- infopath
- SharePoint
---

_Disclaimer: When I first drafted this post back in May 2011 I was not aware that our sister company was developing a competitor to InfoPath. When I did find out, I couldn't say anything due to NDA. I've held off publishing this until that product's been announced in order to be as transparent as possible._

I've worked on numerous InfoPath projects over the last two years, covering each version – 2003, 2007 and 2010. As time has gone on, I've started to develop a twitch whenever someone mentions "InfoPath". Now to be clear, the product has its place and there are times when it is appropriate for use. However there are also things to be **very cautious of**, even to the point where alternatives should be considered such as custom development.

I've tried to tone down this post, but there is a fair bit of frustration here that is hard to eradicate! Here's my top list of gotchas.



# Business logic is hidden



The default way that InfoPath names its data structures as a user creates them looks like this: `field23` or `group22`. The UI never encourages the user to give fields names that describe their purpose. You could argue it actively discourages it by introducing the friction of of a dialog before a field can be renamed. It would be nice if a field was in "rename mode" when a control associated with it was first dragged onto the form!

To add behaviour to a form, the user attaches "rules" to fields. This is an _n_ rules to _1_ field relationship. As it's not unusual to complete the design of a form with up to 50 or even many more fields, many of these rules make up the end result, spread throughout the form. This causes serious maintenance difficulties as the only good UI for finding and working on these rules is on a per-field basis. There is a Logic Inspector "feature" provided with InfoPath but its UI is very poorly designed and there's no way to relate rules to each other. This makes it easy to forget what a rule is there for.

Using InfoPath seems like such an easy solution. In just a few clicks you can have a very powerful form without thinking about it! And therein lies the rub. I'd argue that many people don't put in the effort to properly think through the design of a form, and wind up with unwieldy beasts of complexity. InfoPath provides no assistance in making a form self-documenting and expects you to remember what `field23` is for, or `Rule 5`. Over time we can end up with no-one really knowing how the thing is supposed to work, and forced to unravel a web of rules and fields to find out.



# The loaded gun feature - custom code



The most innocent of requirements can require custom code and sometimes aren't discovered until later down the track once a form has been largely developed and you're locked in to the InfoPath platform. The problem with code is that it requires a developer, adds another level of complexity, and changes the deployment method to SharePoint entirely (requiring involvement from the IT department's SharePoint administrator). Adding code is something that [even Microsoft don't recommend](http://blogs.msdn.com/b/infopath/archive/2011/04/14/working-with-vsto-2008-projects-in-infopath-2010.aspx):



> Create InfoPath forms with as little code as possible, and put the bulk of your code into middle tier services and workflows.



If you're adding code anyway, think about **why** you are using InfoPath and not developing a custom solution. If your deployment target is SharePoint, newer features such as [LINQ to SharePoint](http://msdn.microsoft.com/en-us/library/ee535491.aspx) are very easy to implement.



# Do you prefer a messy or broken form?



If you have developed a form and decide one part of it has to be deleted or replaced with something else then you have two options:




	
  1. You can leave the old fields and groups unused in the underlying data structure but not show them. This means over time you'll have leftover cruft with a purpose that no-one can remember.

	
  2. You can delete them and break the ability to open any older forms. Removing aspects of the data structure is a strict no-no and InfoPath cannot handle this situation.





# The multiple environment nightmare



InfoPath loves to hard-code references to everything, particularly data sources, so if you want to follow a best practice process of deploying to a test environment first, you're asking for more maintenance headaches. Some of the issues are alleviated by deploying a form as a feature that can dynamically correct these references but this requires Visual Studio and locks you into this method of deployment for the life of the form.

Then there's the way republishing the same form across multiple environments will almost certainly cause confusion to both you and SharePoint as column names are not kept identical. This is an issue documented in Microsoft KB article ["Columns published from InfoPath fields are recreated when the same InfoPath form template is re-published to more than one document libraries in a SharePoint site"](http://support.microsoft.com/kb/2554288/).

It's also possible to hit problems with InfoPath's caching. This tries to be helpful but sometimes can be overzealous or just plain gets confused between different environments. Unfortunately it can result in your testers using the test version of a form in production before the update has been deployed!



# Too many bugs



You're never gonna know when one's going to bite you and cost serious time, again and again and again. There are bugs and unexpected behaviour in the client, when deploying forms with code, and in Forms Services. Pretty embarrassing when you think you have a form "just right" and it breaks in front of your demonstration audience.



# The InfoPath sales pitch



Microsoft's selling point is that InfoPath is part of Office and any savvy business user can maintain these forms. However this greatly depends on the complexity of the form and the user's expertise. Many people struggle as forms get more complicated for the reasons mentioned here (plus more), and introducing multiple environments or custom code makes it nigh impossible.

As mentioned earlier, InfoPath has its place. Make sure your reasons for using it are the right ones.



# Enough!



I could go on! But hopefully this gives you some things to think about before falling into the InfoPath deep end. InfoPath has its place for simpler solutions and usually doesn't require a developer resource. Just be careful not to let it cost you dearly later. Fingers crossed that Microsoft will change some fundamental flaws the product has had for a while now in the next version.


