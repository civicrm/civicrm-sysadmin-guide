# Upgrades

Keeping CiviCRM up-to-date helps you take advantage of new features but is critically important when [security updates](https://civicrm.org/security) are issued.

In general:

* The upgrade steps are different depending on your CMS.
* The upgrade steps are the same no matter which versions of CiviCRM you are moving _from_ and _to_ ([with some exceptions](/upgrade/version-specific.md))
* The upgrade steps are the same for small upgrades (e.g. 4.7.10 to 4.7.11) as they are for large upgrades (4.6.7 to 4.7.12).
* You can make big jumps in one upgrade (e.g. 4.5.2 to 4.7.20) without needing to apply all the upgrades in between.

This page provides some of the steps to do before and after an upgrade, but leaves the bulk of the steps up to the CMS specific pages in this chapter.

## Before upgrading {:#before-upgrading}

Before upgrading CiviCRM, follow these steps to ensure the process goes a smoothly as possible.

1. Ensure that your system meets all the [requirements](/requirements.md) for the CiviCRM version to which you are upgrading.

1. Check for any [version-specific upgrade instructions](/upgrade/version-specific.md).

1. **[Backup your site](/setup/backups.md). Very important!**

1. Use your backup to create a _copy_ of your site (directories and database), and ensure that this copied site works correctly. This step is important both for (a) testing that your backup/restore procedure works, and (b) allowing you to test the upgrade in a low-stakes environment.
 
1. Test the upgrade process on your _copied site_ to ensure that it succeeds.

1. [Troubleshoot](/troubleshooting.md) any upgrade problems.

1. Once you have performed a successful *test* upgrade, *then* apply the same steps on your live site.

## Upgrading

These steps vary depending on your CMS. Find the appropriate page in this chapter and follow the steps within it.

## After upgrading {:#after-upgrading}

### Update resource settings

If you are running the upgrade in a different directory from your previous version you may need to update the configured **CiviCRM Resource URL** and **Extension Resource URL** settings. (Missing icons and images, as well as problems with javascript functions and stylesheets are all symptoms indicating that these settings need to be updated.)

1. Go to **Administer CiviCRM » System Settings » Resource URLs**
1. Refer to the field help on that screen for instructions.

### Updating message templates

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


