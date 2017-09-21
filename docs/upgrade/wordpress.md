# Upgrading CiviCRM for WordPress

!!! warning "Use this document to UPGRADE CiviCRM installations on Wordpress to the latest 4.7 release from 4.1.x, 4.2.x, 4.3.x, 4.4.x or an earlier 4.7 version."

    * Installing from scratch? Use the installation instructions for [WordPress](https://wiki.civicrm.org/confluence/display/CRMDOC41/WordPress+Installation+Guide+for+CiviCRM+4.1)

    **IMPORTANT: These instructions are for sites using or upgrading to WordPress only.**


!!! note "Version 4.7 Requirements"

    **Before beginning this upgrade, verify that your server meets the requirements for CiviCRM 4.7**

    * **WordPress 3.4** + : CiviCRM 4.7 is built to run under WordPress 3.4+ and **is not compatible with earlier WordPress versions**.
    * **PHP** : see [CiviCRM PHP Requirements](https://wiki.civicrm.org/confluence/display/CRMDOC/CiviCRM+PHP+Requirements)
    * **PHP memory_limit** set to between 256 and 512 megabytes.
    * **MySQL 5.0.x or higher with InnoDB support** : CiviCRM is compatible the current [generally available MySQL release](http://dev.mysql.com/downloads/mysql). Note that MySQL 5.1 is recommended for new installations and for sites during upgrades as CiviCRM is beginning to use Triggers more (eg to support multi-lingual installations), which require SUPER privileges in MySQL 5.0.
     Configuration variable thread_stack >= 192k.
    * **PCRE with Unicode properties support** ([more info](http://forum.civicrm.org/index.php/topic,9316.0.html)).

**Table of Contents**

* Step-by-step Upgrade Procedures

* 1. Download the most recent CiviCRM Package
* 2. Backup your CiviCRM database and settings file/s
* 3. Unpack the latest package and relocate settings file/s
* 4. Install Localization Files (Optional)
* 5. Run the Upgrade script
* 6. Verify and Update Resource URL Settings
* 7. Update System Workflow Message Templates as Needed
* Upgrade Trouble-shooting

* You do not have sufficient permissions to access this page.
* Reset Your User Session
* Verify and Update Base Directory and Base URL Settings in the Database
* Foreign Key Errors or Warnings During the Database Upgrade
* Upgrade script fails with fatal database-related errors OR reports "Database check failed"
* The menus are wrong, the admin dashboard does not have the correct links and settings, or get an error with CRM_Core_Invoke::require_once()

!!! danger "Upgrade a Copy of Your Site First and Make a Complete Backup"

    We strongly recommend that you create a copy of your existing site (directories and database) - and upgrade that copy first in order to make sure you can complete the upgrade successfully. In any case, you should create a full backup of the installed civicrm directory tree and civicrm database before beginning the upgrade process. Note that not all versions of mysql or phpmyadmin export triggers or stored procedures by default.
    **If you are upgrading a COPY of your production site - make sure the site you are about to upgrade is a fully functioning installation before you begin the upgrade process.** (more info...)


!!! danger "System Administrator Alert"

    **All Cron Jobs on Pre-4.3.x Systems Must be Re-configured**

    * **Upgrades from 4.1.x and prior: All CiviCRM-related cron jobs will stop working as soon as any site is upgraded from 4.1.x and below**. If you are upgrading from a version prior to 4.2, cron jobs will need to be reconfigured using the new [Scheduled Jobs](http://wiki.civicrm.org/confluence/display/CRMDOC43/Managing+Scheduled+Jobs) method.

    * **Upgrades from 4.2.x** : If you are running scheduled jobs using CLI.php, you will need to reconfigure cron to include a password. Scheduled jobs will no longer run if the password is not provided ([learn more](http://wiki.civicrm.org/confluence/display/CRMDOC43/Managing+Scheduled+Jobs)).

# Step-by-step Upgrade Procedures

## Download the most recent CiviCRM Package

* The most up-to-date version of CiviCRM will always be available at [https://civicrm.org/download](https://civicrm.org/download) (though you may have to endure a fundraising pitch).
* Select the currently available civicrm-4.7 file for Wordpress. Example: **civicrm-4.7.x** -wordpress.zip*
* If you wish to localize your installation you also need to download the l10n tarball, example: civicrm-4.7.x-l10n.tar.gz. (Take a look at the [installation instructions](https://wiki.civicrm.org/confluence/display/CRMDOC/WordPress+Installation+Guide+for+CiviCRM) regarding where these files need to be unpacked.)

## Backup your CiviCRM database and settings file/s

Refer to the [MySQL reference manual](http://dev.mysql.com/doc/refman/5.1/en/mysqldump.html) or the [phpMyAdmin documentation](http://wiki.cihar.com/pma/FAQ_6.4) if you need information on backing up your database. Note that not all versions of mysql or phpmyadmin export triggers or stored procedures by default.

**Make a backup/copy of civicrm settings file** :

1. For Versions installed prior to 4.7 - <wordpress_root>/wp-content/plugins/civicrm/civicrm.settings.php. (This will be useful for relocating.)

    1. For Versions installed 4.7+ - /wordpress_root/content-dir/uploads/civicrm/civicrm.settings.php
        1. The installer in 4.7 does not assume that the content-dir is wp-content. It probably is, but can be renamed/moved as detailed here [https://codex.wordpress.org/Editing_wp-config.php#Moving_wp-content_folder](https://codex.wordpress.org/Editing_wp-config.php#Moving_wp-content_folder)
1. If you are using the <wordpress_root>/wp-content/plugins/civicrm/civicrm/settings_location.php file in your implementation, make a copy of this as well.

**CiviCRM will not run properly if files from previous version are present after the upgrade.** Make sure you have a good backup of your complete previous version installation and then delete /_wp-content/plugins_/civicrm.

!!! warning

    Make sure you have logged in to Wordpress BEFORE deleting the old files. Do not log out until the entire process is complete.


## Unpack the latest package and relocate settings file/s

* Unpack the files into <wordpress home>/wp-content/plugins/.
```
// Modify this line to use your real WordPress root folder and the exact name of your content-dir (typically wp-content)
cd <wordpress home>/<content-dir>/plugins
// Modify this line with the actual downloaded package file name
unzip civicrm_download_file.zip
```
* For Versions installed prior to 4.7 - Relocate (paste) the backed-up civicrm.settings.php file(in step 2) inside <wordpress home>/wp-content/plugins/civicrm/

* If you have upgraded from a CiviCRM 4.1 version, check the contents of civicrm.settings.php. Make sure at the very end of the file these 2 lines exist, and if not, add them.

```
require_once 'CRM/Core/ClassLoader.php';
CRM_Core_ClassLoader::singleton()->register();
```

* If you are using the <wordpress_root>/<content-dir>/plugins/civicrm/civicrm/settings_location.php file in your implementation, relocate this file from your back-up.

## Install Localization Files (Optional)

If your site requires the localization files, download the **civicrm-4.7.x-l10n.tar.gz** from [https://civicrm.org/download](https://civicrm.org/download)

These files must be untarred under <wordpress home>/wp-content/plugins/civicrm/. Please note the tar.gz file only contains /civicrm not /civicrm/civicrm

```
// Modify this line to use your real WordPress root folder and the exact name of your content-dir (typically wp-content)
cd <wordpress home>/<content-dir>/plugins/civicrm
// Modify this line with the actual downloaded package file name
tar -xf ../civicrm-4.7.x-l10n.tar.gz
```

If you don't have SSH access and your IP panel does not recognize tar files, you will need to convert it to a zip file.

* Uncompress **civicrm-4.7.x-l10n.tar.gz**
* Uncompress **civicrm-4.7.x-l10n.tar**. You should now have a **civicrm** directory with 2 subdirectories **l10n** and **sql**.
* Rename **civicrm** to **civicrm2**.
* Create an empty **civicrm** directory.
* Move the **civicrm2** directory (with the 2 subdirectories) into your new **civicrm** directory.
* Rename **civicrm2** to **civicrm**.
* Zip the **civicrm** directory and name it **civicrm-4.7.x-l10n.zip**
* Now, you can upload **civicrm-4.7.x-l10n.zip** into <wordpress home>/wp-content/plugins/
* Unzip **civicrm-4.7.x-l10n.zip** and continue with Step 5.

## Run the Upgrade script

**This step is required for ALL upgrades.**

!!! danger "MySQL 4.0 and 4.1 are Not Supported"

    Support for MySQL versions 4.0 and 4.1 has been discontinued. This means that you may encounter various issues and/or unexpected behavior if you attempt to run CiviCRM under these versions, and the CiviCRM engineering team will not provide support for debugging or resolving these issues. You are strongly encouraged to upgrade to the current generally available release of MySQL.


!!! danger "Clear Cache"

    Delete all files in <wordpress>/<content-dir>/plugins/files/civicrm/templates_c/ and clear browser cache

    Note for installs starting in CiviCRM 4.7 the path is now <wordpress-root>/<content-dir>/uploads/civicrm/templates_c


* Point your web browser to the following URL (you should already be logged in to Wordpress with administrator-level permissions):

```
http://<your_wordpress_home>/wp-admin/admin.php?page=CiviCRM&q=civicrm/upgrade&reset=1
```

* You should see the Upgrade screen.
* If you are ready to upgrade, click the **Upgrade Now** button.
* You should see the message **Upgrade successful** when the upgrade completes.
    * If you receive any errors during the process, please note down the exact error message, and check for solutions on the [community support forum](http://forum.civicrm.org/).
* Now click the **Return to CiviCRM home page** link. This will rebuild CiviCRM menus automatically and return you to the CiviCRM home dashboard.

## Verify and Update Resource URL Settings

If you are running this installation in a different directory from your previous version you may need to update the configured **CiviCRM Resource URL** and **Extension Resource URL** (Missing icons and images, as well as problems with javascript functions and stylesheets are all symptoms indicating that these settings need to be updated.)

* Go to **Administer** » **System**** Settings **»** Resource URLs**
* Refer to the field help on that screen for instructions.

## Update System Workflow Message Templates as Needed

If your organization has modified the default versions of System Workflow message templates, then the changes and bug fixes included in an upgrade will need to be merged with your modified versions. ([learn more ...](https://wiki.civicrm.org/confluence/display/CRMDOC/Updating+System+Workflow+Message+Templates+after+Upgrades+-+method+1+-+kdiff))

## Upgrade Trouble-shooting

Check this section for answers to upgrade problems. If your problem isn't addressed here, check out the **[Installation and Configuration Trouble-shooting](https://wiki.civicrm.org/confluence/display/CRMDOC41/Installation+and+Configuration+Trouble-shooting)** page for additional resources.

#### You do not have sufficient permissions to access this page.

If you get this error when trying to run the upgrade script(step 4), you most likely deactivated CiviCRM plugin during the upgrade process. You just have to visit <wordpress_home>/wp-admin/plugins.php and activate the plugin, and then proceed with step 4 .

#### Reset Your User Session

If you are getting foreign key constraint errors when trying to add or modify records, you may need to reset your user session.

* Temporarily enable CiviCRM debug features:
    * Go to **Administer CiviCRM** » **System Settings** » **Debugging and Error Handling**
    * Set **Enable Debugging** to Yes and click Save.
* Click the Administer CiviCRM menu (or any other CiviCRM menu item). After the page is loaded, add an additional query string value (sessionReset=2) to the URL in your browser's location bar, and reload the page.
```
// Example URL
http://...../?q=civicrm/admin&sessionReset=2
```
* Now reset **Enable Debugging** to No and click Save.

!!! danger "Do Not Leave Debug Features Enabled for a Public Site"
 Debugging should be disabled for publicly available sites as it may allow browsers to view system configuration information. |

#### Verify and Update Base Directory and Base URL Settings in the Database

If you are seeing problems with missing images or page styling, you may need to adjust the Base Directory and Base URL Settings in the database. You can do this from:
**Administer CiviCRM** » **System Settings** » **Cleanup Caches and Update Paths**

#### Foreign Key Errors or Warnings During the Database Upgrade

Foreign keys may have been assigned different names on some installations. Also, different versions of MySQL handle the dropping and adding of constraint checks differently. Try [this procedure](https://wiki.civicrm.org/confluence/display/CRMDOC41/Ensuring+Schema+Integrity+on+Upgrades) or [this procedure (on the forum)](http://forum.civicrm.org/index.php/topic,4259.msg21599.html#msg21599) to reload your data into a new 4.0 database structure if you are having this type of issue with upgrading your database.

#### Upgrade script fails with fatal database-related errors OR reports "Database check failed"

Download and run [Database Troubleshooting Tools](https://wiki.civicrm.org/confluence/display/CRMDOC41/Database+Troubleshooting+Tools) to test the current state of the database and provides a diagnosis. The tools suite also includes a repair facility.

#### The menus are wrong, the admin dashboard does not have the correct links and settings, or get an error with CRM_Core_Invoke::require_once()

You can re-build the menu/admin dashboard by visiting the following url -

`http:``//<your_wordpress_home>/wp-admin/admin.php?page=CiviCRM&q=civicrm/menu/rebuild&reset=1`