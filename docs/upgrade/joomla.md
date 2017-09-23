# Upgrading CiviCRM for Joomla

!!! note "Version 4.7 Requirements"

    **Before beginning this upgrade, verify that your server meets the requirements for CiviCRM 4.7.**

    * **Joomla Version.** : CiviCRM 4.7 has been built to run under Joomla 2.5.x or 3.x.x. **4.7**  **is not compatible with Joomla 1.0.x and Joomla 1.5.x sites**.
    **PHP:** See [CiviCRM PHP Requirements](https://wiki.civicrm.org/confluence/display/CRMDOC/CiviCRM+PHP+Requirements)
    * **PHP memory_limit** set to between 256 and 512 megabytes.
    * **MySQL 5.1.x or higher with INNODB support** : CiviCRM is compatible the current [generally available MySQL release](http://dev.mysql.com/downloads/mysql). Note that MySQL 5.1 is recommended for new installations and as sites are upgraded as CiviCRM is beginning to use Triggers more (eg to support multi-lingual installations), which require SUPER privileges in MySQL 5.0.
     Configuration variable thread_stack >= 192k.
    * **PCRE with Unicode properties support** ([more info](http://forum.civicrm.org/index.php/topic,9316.0.html)).

!!! danger "Upgrade a Copy of Your Site First and Make a Complete Backup"

    We strongly recommend that you create a copy of your existing site (directories and database) - and upgrade that copy first in order to make sure you can complete the upgrade successfully. In any case, you should create a full backup of the installed civicrm directory tree and civicrm database before beginning the upgrade process. Note that not all versions of mysql or phpmyadmin export triggers or stored procedures by default.


The steps below assume you are already running Joomla 3.x. If you have not yet upgraded to 3.x, you must do that before installing CiviCRM v4.x. Also note that if you used the jUpgrade extension to manage your Joomla upgrade, it would have created your upgraded site in a subdirectory (jupgrade/ by default). If you use that subdirectory site to upgrade CiviCRM you will need to alter your civicrm.settings.php file prior to the upgrade (to reflect the subdirectory) and after you complete the upgrade and move your site to a root directory (to again reflect the correct directory location).

## Download and Un-tar CiviCRM Code

All CiviCRM code and packages used by CiviCRM (such as PEAR libraries) are included in the compressed CiviCRM distribution files ('tarballs'). Follow these steps to download and install the codebase:

* Download the [appropriate archive file](http://sourceforge.net/projects/civicrm/files/) with your browser. Archive file-names include the CiviCRM version. For example, **civicrm-4.7.xxx-joomla.zip**.
* You may notice a file on the named something like **civicrm-4.7.xxx-joomla-alt.zip** the -alt version DOES NOT require .zip functions compiled into PHP by your hosting company. If you get errors like "Your PHP version is missing zip functionality. Please ask your system administrator / hosting provider to recompile PHP with zip support." - Try the -alt version.
* Upload this file to your **JOOMLA_ROOT/tmp/**. Unzipping will create a directory called: com_civicrm.

## Backup your CiviCRM database and settings file

Refer to the [MySQLDump documentation](http://dev.mysql.com/doc/refman/5.1/en/mysqldump.html) or [phpMyAdmin documentation](http://wiki.cihar.com/pma/FAQ_6.4) if you need information on backing up your database. Note that not all versions of mysql or phpmyadmin export triggers or stored procedures by default.

Also make a backup/copy of civicrm settings files:

1. <joomla_root>/components/com_civicrm/civicrm.settings.php and
1. <joomla_root>/administrator/components/com_civicrm/civicrm.settings.php

#### Customizations

If you have customized CiviCRM through PHP/template (tpl)/css overrides or hooks, you will need to update the code to the upgraded version. Depending on the extent and function-critical nature of your customizations, you may want to upgrade your installation in a development environment first where you can review each modified file, bring it current to the new codebase, and thoroughly test before implementing in a live environment.

If you modified any core files, also be prepared to backup and update them to the new release you are implementing.

## Install the Extension

* Login to your Joomla Administrator site.
* Go to Extensions » Manage
* Use the **Install from Folder** tab and enter the full path to the un-zipped **com_civicrm** directory, which should be something like **JOOMLA.x_ROOT/tmp/com_civicrm**. If your temp directory is configured correctly you should only need to add "com_civicrm" to the prepopulated path.
* You should see "CiviCRM successfully installed" message.

!!! warning

    *If you get the following error when installing or upgrading CiviCRM for Joomla!,* download and use the civicrm-4.7.x-joomla-alt.zip package.
    **Your PHP version is missing zip functionality. Please ask your system administrator/hosting provider to recompile PHP with zip support.**

The steps above will install CiviCRM directly on top of your existing installation. If you run into any issues, such as the component not appearing to reflect the new version, you may need to first uninstall the existing version and then install the new version. Uninstalling CiviCRM will remove the component files, but will not impact your database in any way.

## Run the Database Upgrade script

!!! forbidden "Update requiredments"
    Support for MySQL versions prior to 5.0 has been discontinued. Delete all cache files in [yoursite]/media/civicrm/templates_c on your server before proceeding with the update script.


**After the component installation completes click the link to run the database upgrade script.** You may also point your web browser to the following URL (you should already be logged in to Joomla with administrator-level permissions):

```
http://<your_joomla_home>/administrator/index.php?option=com_civicrm&task=civicrm/upgrade&reset=1
```

You should see an **Upgrade successful** message when the upgrade completes.

* If you receive any errors during the process, please note the exact error message and check for solutions on the community support forum.
* Now click the **Return to CiviCRM home page** link.
* You should be up and running with the latest CiviCRM version. Confirm by checking the version and revision in the page footer. Note that you may need to refresh the browser screen a couple times to clear out your local cache and ensure the layout loads correctly.
* Take some time to browse the CiviCRM features that your organization uses. If you notice unexpected behaviors or error messages, refer to the trouble-shooting section below.

## Verify and Update Configuration Settings

If you are running this installation in a different directory from your old site you may need to update the **CiviCRM Resource URL** setting (missing icons and images, as well as problems with javascript functions and stylesheets are all symptoms that this setting needs to be updated).

* Go to **Administer** » **System Settings** » **Resource URLs**
* Refer to the field help on that screen for instructions.

With each new release of CiviCRM there may be new sub-components and configuration settings available. Visit **Administer » System Settings** and review each section to make sure your system is configured as desired. If you uninstalled CiviCRM before installing the new version please be sure to review each setting carefully as it's possible some of your settings were reset to the default options.

## Restore settings file changes

Review the backup versions of the files below and compare them with the new versions just installed. In particular, make sure your site key value was retained and any of the configuration options found in those files are still intact.

1. <joomla_root>/components/com_civicrm/civicrm.settings.php
1. <joomla_root>/administrator/components/com_civicrm/civicrm.settings.php

## Update System Workflow Message Templates as Needed

If your organization has modified the default versions of System Workflow message templates, then the changes and bug fixes included in an upgrade will need to be merged with your modified versions. ([learn more ...](https://wiki.civicrm.org/confluence/display/CRMDOC/Updating+System+Workflow+Message+Templates+after+Upgrades+-+method+1+-+kdiff))

## Troubleshooting

### Reset Session

If you are seeing unexpected behavior after completing the upgrade, you may need to reset your session. First log-out of your Administrator session and log back in. This may resolve the issues. If this doesn't help, use this procedure to force a session reset:

1. Temporarily enable CiviCRM debug features:
    * Go to **Administer** » **System Settings** » **Debugging and Error Handling**
    * Set **Enable Debugging** to Yes and click Save.
    
1. Click the Administer CiviCRM menu (or any other CiviCRM menu item). After the page is loaded, add an additional query string value (&sessionReset=2) to the URL in your browser's location bar, and reload the page.
    ```
    // Example URL
    http://.../administrator/index.php?option=com_civicrm&task=civicrm/admin/setting&reset=1&sessionReset=2
    ```
    
1. Clear template cache by adding an additional query string value (&directoryCleanupReset=1)
    ```
    // Example URL
    http://.../administrator/index.php?option=com_civicrm&task=civicrm/admin/setting&reset=1&directoryCleanup=1
    ```
    
1. Now set **Enable Debugging** to No and click Save.

    !!! danger "Do Not Leave Debug Features Enabled for a Public Site"
         Debugging should be disabled for publicly available sites as it may allow browsers to view system configuration information.

### Upgrade script fails with fatal database-related errors OR reports "Database check failed"

Download and run the [Database Troubleshooting Tools](https://wiki.civicrm.org/confluence/display/CRMDOC41/Database+Troubleshooting+Tools) to test the current state of the database and provides a diagnosis. The tools suite also includes a repair facility.
 (Note ... the troubleshooting tools do not work with version 4.7 - this item should be deleted shortly)

### Remove cached copies of old templates

It may be that the install did not clear out the template cache. Try removing the cache contents. For example, with Linux, use with a command like:

```
rm -r JOOMLA_ROOT/media/civicrm/templates_c/en_us/en_us
```

This will force CiviCRM to load the new templates rather than cached copies of the old ones.

### Out of Date Joomla! Version

!!! forbidden "Joomla 2.5 reached End of Life (EOL)"

    * **Joomla 2.5 reached _End of Life_ (EOL) in 2014 and is no longer supported by the Joomla! Project.**
    * **While CiviCRM 4.7 supports Joomla 2.5.x or 3.x.x, i**** t is strongly recommended by the Joomla! Project that 2.5 users upgrade to the most current version of 3.x ([https://docs.joomla.org/Joomla!_CMS_versions](https://docs.joomla.org/Joomla!_CMS_versions)).**

### Cron Job Reconfiguration Necessary when upgrading from < CiviCRM 4.2

!!! danger "System Administrators Alert"

    **All Cron Jobs Must be Re-configured**

    * **Upgrades from 4.1.x and prior: All CiviCRM-related cron jobs will stop working as soon as any site is upgraded from 4.1.x and below**. If you are upgrading from a version prior to 4.2, cron jobs will need to be reconfigured using the new [Scheduled Jobs](http://wiki.civicrm.org/confluence/display/CRMDOC43/Managing+Scheduled+Jobs) method.

    * **Upgrades from 4.2.x** : If you are running scheduled jobs using CLI.php, you will need to reconfigure cron to include a password. Scheduled jobs will no longer run if the password is not provided ([learn more](http://wiki.civicrm.org/confluence/display/CRMDOC43/Managing+Scheduled+Jobs)).

### Need More Help?

If you have any problems with these procedures, try searching the [community forums](http://forum.civicrm.org/) and this wiki for solutions. If you've gotten an error message, use that message in your search. If you can not resolve the problem, then post your problem to the forum. Be sure to include the CiviCRM version and revision you are upgrading FROM and TO; your Joomla version; your PHP and MySQL versions; the steps you've taken and a the exact error message or problem that resulted.