# Updating System Workflow Message Templates
              after Upgrades - method 1 - kdiff

If your organization has modified the default versions of System Workflow message templates, then the changes and bug fixes included in an upgrade will need to be merged with your modified versions.

There are a few methods mentioned in this blog [https://civicrm.org/blogs/andrewhunt/upgrade-custom-templates](https://civicrm.org/blogs/andrewhunt/upgrade-custom-templates)

The focus here is on the one that seems most low tech - [http://sourceforge.net/projects/kdiff3](http://sourceforge.net/projects/kdiff3)

You can identify altered templates on the System Workflow Messages screen. The altered ones show 'Revert to default' & 'View Default'. You need to view the new Default versions, & compare them with what you see in the 'Edit' linked page (which will be the latest version used by your organization).

![](https://wiki.civicrm.org/confluence/download/attachments/135135258/TemplatesToUpdate.jpg?version=1&modificationDate=1399945725000&api=v2)

So, for #1 e.g.

We go into the'View Default' link & copy the text & html versions into separate text documents - e.g ContributionOnlineHTMLDefault.txt & ContributionOnlineHTMLText.txt. Then put the customised versions from the Edit tab into separate documents e.g ContributionOnlineHTMLCustom.txt & ContributionOnlineTEXTCustom.txt

![](https://wiki.civicrm.org/confluence/download/attachments/135135258/CopyCustomisedText.jpg?version=1&modificationDate=1399945725000&api=v2)

Select the files to compare in kdiff (I didn't click merge but I think it is better to as it creates the merged copy down the bottom)

![](https://wiki.civicrm.org/confluence/download/attachments/135135258/Compare2FilesInKdiff.jpg?version=1&modificationDate=1399945725000&api=v2)

You then get this view - you can see here I loaded the default on the left & the custom on the right - I can scroll up & down the changes using the blue arrow buttons. When I have a change highlighted I choose 'A' to choose side A, B to choose side B. The merged version shows up down the bottom & you can copy this back into CiviCRM & test

![](https://wiki.civicrm.org/confluence/download/attachments/135135258/Compare%26Merge.jpg?version=1&modificationDate=1399945725000&api=v2)