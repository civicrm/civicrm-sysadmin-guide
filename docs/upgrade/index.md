# Upgrades

Keeping CiviCRM up-to-date helps you take advantage of new features but is critically important when [security updates](https://civicrm.org/security) are issued.

In general:

* The upgrade steps are different depending on your CMS.
* The upgrade steps are the same no matter which versions of CiviCRM you are moving _from_ and _to_ ([with some exceptions](/upgrade/version-specific.md))
* The upgrade steps are the same for small upgrades (e.g. 4.7.10 to 4.7.11) as they are for large upgrades (4.6.7 to 4.7.12).
* You can make big jumps in one upgrade (e.g. 4.5.2 to 4.7.20) without needing to apply all the upgrades in between.
* If you are starting from a version prior to 4.2.9 you should upgrade to the [latest 4.2 version](https://sourceforge.net/projects/civicrm/files/civicrm-stable/4.2.19/) as an interim step, as the latest version does not contain upgrade code going back that far.

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

### Flush caches

If you ran the upgrade on the command-line, you will need to flush caches by running `cv flush`. The upgrade web interface will take care of this automatically.

### Evaluate new permissions

The CiviCRM post-upgrade messages should alert you if new CMS permissions are added.  If so, [review and configure the CMS permissions](https://docs.civicrm.org/user/en/latest/initial-set-up/permissions-and-access-control/#cms-permissions).

### Update resource settings

If you are running the upgrade in a different directory from your previous version you may need to update the configured **CiviCRM Resource URL** and **Extension Resource URL** settings. (Missing icons and images, as well as problems with javascript functions and stylesheets are all symptoms indicating that these settings need to be updated.)

1. Go to **Administer CiviCRM » System Settings » Resource URLs**
1. Refer to the field help on that screen for instructions.

### Updating system workflow message templates

[System workflow message templates](https://docs.civicrm.org/user/en/latest/email/message-templates/) are templates for the various emails that CiviCRM sends regularly (e.g. contribution receipts, event registration confirmations). CiviCRM occasionally makes changes to these templates in new releases. But *users* can also make changes to these templates. If a user has customized a template, then CiviCRM will stop applying updates to that template during version upgrades. This behavior is good because it preserves your customizations, but bad because you'll miss out on template updates from CiviCRM core (which are sometimes important).

For each version upgrade you perform, follow these steps to merge your template customizations with CiviCRM's template changes.

1. Determine your currently-installed CiviCRM version (after your upgrade) *and* your previously-installed CiviCRM version (before your upgrade).

1. Assess the changes made to your templates &mdash; both by your users and by the CiviCRM upgrade.

    The following two steps can be done in either order. If you have relatively few user customizations, then look through them first. Conversely, if your version upgrade spans relatively few releases, then look through CiviCRM's changes first.

    * See which templates _users_ have customized.

        Navigate to **Administer > Communication > Message Templates** and click the **System Workflow Message** tab.

        Identify any customized templates by looking for rows which display *"Revert to default"* and *"View Default"* links.

        ![System workflow templates screen showing user-customized templates which have "Revert to default" links next to them](/img/TemplatesToUpdate.jpg)

    * See which templates _CiviCRM_ has changed.

        Within your installed CiviCRM files, navigate to `CRM/Upgrade` and look through the `*.msg_template` folders. Make sure to examine all versions between your previously-installed version and your currently-installed version.

        !!! tip "For example"
            By looking in the `4.7.23.msg_template` folder, we can see that the "Contribution invoice receipt" template was changed in version 4.7.23. So, if you are upgrading from a version prior to 4.7.23 to a version after 4.7.23, then you'll need to note this template as one which has changed.

1. Identify templates which have been changed by _both_ users and CiviCRM. (Mostly commonly, you won't have any such templates and you can stop here!)

    For each of these templates with conflicting changes, perform the remaining steps.

    !!! note
        Each message template has _two_ separate pieces of text &mdash; the HTML version and the text version. Take note of conflicting changes to each of these types, as you'll need to process them separately.   

1. Let's pause here and explain a little theory...

    When a piece of text is changed in two divergent ways, there are _three_ versions of this text which matter: **(A)** the "base" version before any changes were made, **(B)** the version changed by one party (e.g. _users_), and **(C)** the version changed by another party (e.g. _CiviCRM core_).

    Our goal here is to _merge_ **(B)** and **(C)** to produce a new version which includes all the changes.

    In the best case scenario, the changes in **(B)** and **(C)** were made to completely independent parts of the text. In this case, the merge process is straightforward and can be done automatically with the right software. *(But note that we need to have the **(A)** version to tell if this scenario exists.)*

    In a less ideal scenario, the changes in **(B)** and **(C)** targeted the exact same text, changing it in different ways. Here, there are said to be "merge conflicts".

1. Let's use a 3-way diff/merge application to merge the changes. [KDiff3](http://sourceforge.net/projects/kdiff3) is a cross-platform application that does exactly this. Install KDiff3 now, or continue by using a similar application of your choice.

1. Find the base version, **(A)**, of the template before any changes were made by users or your CiviCRM upgrade. Again, look in the `CRM/Upgrade` directory. Find the most recent version of the template *before* the CiviCRM version that you had prior to the upgrade.

1. Find the version of the template, **(B)**, changed by your users.

    Within the Message Templates screen click **Edit** next to the customized template.

    ![Screenshot of the Edit Message Template screen](/img/edit-message-template.png)

    Copy the text from this template into a temporary file on your computer.

1. Find the version of the template, **(C)**, changed by your CiviCRM upgrade by looking in the `CRM/Upgrade` directory, as before.

1. Load all three files into your diff/merge program.

    ![Screenshot of opening three files in KDiff3](/img/KDiff3-open-files.png)

    In KDiff3, it's helpful to check the **Merge** box and specify an output file, as shown in the screenshot above.

1. In most cases, the diff/merge application will be able to automatically merge changes from **(B)** and **(C)**. However in some rare cases you'll need to use the diff/merge program to manually resolve merge conflicts.

    When you're done merging changes (either automatically or manually), you'll have an output which you can save to a separate file.

    ![Screenshot of KDiff3 displaying the result of a three-way merge](/img/KDiff3-merge.png)

1. Finally, copy the output of the merge back into Message Template screen within CiviCRM, and save.
