# Upgrading CiviCRM for Drupal 7

!!! warning "Use this document to UPGRADE CiviCRM installations on Drupal 7 to the latest 4.7 release from version 2.2 and above."

    * Installing from scratch? Use the installation instructions for [Drupal 7](https://wiki.civicrm.org/confluence/display/CRMDOC/Drupal+Installation+Guide+for+CiviCRM+4.5+-+Drupal+7)
    * Upgrading from a version prior to 2.2? First upgrade to 2.2 [using these instructions](https://wiki.civicrm.org/confluence/display/CRMDOC22/Upgrade+Drupal+Sites+to+2.2).

    {*}IMPORTANT: These instructions are for sites using or upgrading to Drupal 7 only. Before considering an upgrade of a production Drupal 6 site, verify that other Drupal modules you may be using are either compatible with Drupal 7.x OR that there is a Drupal 7 version you can upgrade to.

    **NOTE: When you upgrade Drupal from 6.x to 7.x, the official instructions on Drupal.org say to disable all non-core modules. However, we recommend that you do NOT disable CiviCRM. If CiviCRM is disabled and you upgrade to Drupal 7.0, you will then**  **not**  **be able to upgrade CiviCRM. If you have done this, the only option is to install CiviCRM on a blank database, and then manually edit civicrm.settings.php to point to your real database and then proceed with the upgrade. See below for more information. Or, you can use drush civicrm-upgrade-db.**


!!! note "Version 4.7 Requirements"

    **Before beginning this upgrade, verify that your server meets the requirements for CiviCRM 4.7**

    * **CiviCRM 2.2.x+** : You must be running CiviCRM 2.2.x or higher to use this upgrade. Running a version prior to 2.2? First upgrade to 2.2 [using these instructions](https://wiki.civicrm.org/confluence/display/CRMDOC22/Upgrade+Drupal+Sites+to+2.2)
    * **Drupal 7.x** : CiviCRM 4.7 has been built to run under Drupal 7. It may run on Drupal 6, however **it is not compatible with Drupal 5 sites**.
    * **PHP:** see [CiviCRM PHP Requirements](https://wiki.civicrm.org/confluence/display/CRMDOC/CiviCRM+PHP+Requirements)
    * **PHP memory_limit** set to between 256 and 512 megabytes.
    * **MySQL 5.1.10 or higher with InnoDB support** : CiviCRM is compatible the current [generally available MySQL release](http://dev.mysql.com/downloads/mysql). Triggers (eg to support multi-lingual installations), require SUPER privileges in MySQL 5.0.
     Permissions needed for CiviCRM 4.7 upgrade on MySQL 5.1: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, CREATE VIEW, TRIGGER, CREATE ROUTINE, ALTER ROUTINE. (CREATE VIEW, TRIGGER, CREATE ROUTINE, ALTER ROUTINE are new requirements for update to CiviCRM 4.7, the others were needed before on 4.2.x.)
     Configuration variable thread_stack >= 192k.
    * **PCRE with Unicode properties support** ([more info](http://forum.civicrm.org/index.php/topic,9316.0.html)).


!!! danger "Upgrade a Copy of Your Site First and Make a Complete Backup"

    We strongly recommend that you create a copy of your existing site (directories and database) - and upgrade that copy first in order to make sure you can complete the upgrade successfully. In any case, you should create a full backup of the installed civicrm directory tree and civicrm database before beginning the upgrade process. Note that not all versions of mysql or phpmyadmin export triggers or stored procedures by default. Ensure that your backup restores to a working CiviCRM system before commencing the upgrade.
    **If you are upgrading a COPY of your production site - make sure the site you are about to upgrade is a fully functioning installation before you begin the upgrade process.** (more info...)


!!! danger "Format of Setting Overrides has changed"

    **Background:** Settings are usually stored in the civicrm_setting table, where they're easily maintained. But there are occasions when developers might find it useful to override them. For instance, the development environment might mirror the production environment, except for a handful of settings. Adding a few lines to the civicrm.settings.php file can be much easier and less error-prone than changing production settings for development.

    The settings in the civicrm_setting table are keyed on _domain_, _group_, and _name_. Here's an example using the CiviCRM 4.1 mechanism:

    ```
    define('URL Preferences.customCSSURL', 'http://example.com/css');...
    ```

    The _domain_ is determined by the civicrm.settings.php file, the _group_ is 'URL Preferences', the _name_ is 'customCSSURL', and the _value_ is '[http://example.com/css](http://example.com/css)'. If you're uncertain of the valid values these can take, inspect the civicrm_setting table.

    **The change:** In CiviCRM 4.3 and later, the format has changed to:

    ```
    global $civicrm_setting;
    ```

    ```
    $civicrm_setting['URL Preferences']['customCSSURL'] = 'http://example.com/css';...
    ```

!!! danger "All Cron Jobs Must be Re-configured"

    **System Administrators Alert**

    * **Upgrades from 4.1.x and prior: All CiviCRM-related cron jobs will stop working as soon as any site is upgraded from 4.1.x and below**. If you are upgrading from a version prior to 4.2, cron jobs will need to be reconfigured using the new [Scheduled Jobs](https://wiki.civicrm.org/confluence/display/CRMDOC/Managing+Scheduled+Jobs) method.

    * **Upgrades from 4.2.x** : If you are running scheduled jobs using CLI.php, you will need to reconfigure cron to include a password. Scheduled jobs will no longer run if the password is not provided ([learn more](https://wiki.civicrm.org/confluence/display/CRMDOC/Managing+Scheduled+Jobs)).

# Step-by-step Upgrade Procedures

## Download the most recent CiviCRM Package

* From [http://sourceforge.net/projects/civicrm/files/civicrm-latest/](http://sourceforge.net/projects/civicrm/files/civicrm-latest/)
* Select the currently available civicrm-4.7 tarball for Drupal. Example: **civicrm-4.7.x** -drupal.tar.gz*

## If Using Localization download the latest version of the localization files

See the [CiviCRM Localisation](http://wiki.civicrm.org/confluence/display/CRMDOC40/CiviCRM+Localisation) page about how to install files for running CiviCRM in languages other than American English.

## Take the Site Offline (for production site upgrades)

When upgrading a production site, it is recommended that you take your site offline during the upgrade process.
**Administration » Configuration » Development » Maintenance mode**

## Ensure that CiviCRM is not your Drupal homepage

Due to changes in Dashboard files from 3.0 to 3.1, you need to ensure that your Drupal homepage is not the CiviCRM homepage. If your site does use CiviCRM as its front page, change it by clicking on:
**Administration » Configuration » System » Site Information**
 Then ensure that the "Default front page" value is not civicrm (change it to node if necessary). If you made a change, click Save configuration

!!! warning

    NB: there are a variety of modules that may affect the Drupal homepage. Consult the appropriate module's documentation if you are using them to set the homepage to civicrm for the administrative action required to change to a different non-CiviCRM homepage for the upgrade process.


## Disable all CiviCRM integration modules (NOT CiviCRM itself)

If you have modules installed in your Drupal site that integrate with or extend CiviCRM functionality, you should disable them prior to running the upgrade script. This will prevent modules that are not compatible with the new version from triggering errors in the upgrade process.

**Administration » Modules**

Note which modules in the CiviCRM section of the modules listing are currently enabled (taking a screenshot is one easy way to do this). Now un-check the Enabled box for ALL modules in the CiviCRM section of the modules listing EXCEPT for CiviCRM itself. Click Save.

!!! warning

    **Do NOT disable CiviCRM itself. Only disable other modules in the CiviCRM section of the modules page. The upgrade will NOT run if CiviCRM is disabled.**


## Backup your CiviCRM database

Refer to the [MySQL reference manual](http://dev.mysql.com/doc/refman/5.1/en/mysqldump.html) or the [phpMyAdmin documentation](http://docs.phpmyadmin.net/en/latest/faq.html?highlight=backup) if you need information on backing up your database. Note that not all versions of mysql or phpmyadmin export triggers or stored procedures by default.

This backup is what you'll rely on if the upgrade doesn't go as planned - before you continue, make sure that you're able to restore a working CiviCRM site from this backup.

## Backup and then delete all previous version code files

**CiviCRM will not run properly if files from previous version are present after the upgrade.** Make sure you have a good backup of your complete previous version installation and then delete _/sites/all/modules/civicrm_.

!!! danger

    **Be sure that your backup copy of the previous version codebase is NOT located below the <drupal home>/modules or <drupal home>/sites/all/modules directory.** For example, placing your codebase backup in <drupal home>/modules/civicrm.bak... will cause errors (Drupal executes any *.module files that are found in the modules or sites/all/modules directory tree).


!!! warning

    Make sure you have logged in to Drupal BEFORE deleting the old files. Do not log out until the entire process is complete.


## Unpack the latest package and verify permissions

* Unpack the files into _<drupal home>/sites/all/modules/_.
```
// Modify this line to use your real drupal root
cd <drupal home>/sites/all/modules
// Modify this line with the actual downloaded package file name
tar -xzf civicrm_download_file.tgz
```
* You should now have a new civicrm directory tree under the _<drupal home>/sites/all/modules/_ directory.
* Ensure that your Drupal _files_directory is writeable by the webserver. You can use the following command to set proper permissions:
```
// Modify this line to use your real drupal root directory
cd <drupal home>/sites/default

// The following two lines set basic reasonable web permissions:
// 0755 is Equivalent to chmod u=rwx,g=rx,o=rx
// 0644 is Equivalent to chmod u=rw,g=r,o=r
find . -type d -exec chmod 0755 {} \;
find . -type f -exec chmod 0644 {} \;

// Give the web user permissions to
// read and write recursively inside the files directory.
chmod -R u+rw files
```

## Update the civicrm.settings.php file

**(Only) if your are upgrading from a version of CiviCRM less than 4.0**: CiviCRM 4.0 and above requires definitions in civicrm.settings.php which previous versions of CiviCRM did not have. There are two methods to put these **required** definitions into your install:

1. Create a "dummy" database and install CiviCRM fresh into that. You will need to rename or move your existing civicrm.settings.php. Running the installer anyhow will automatically create a correct civicrm.settings.php file. Then either replace that dummy database with your real one and proceed with the upgrade OR edit the new civicrm.settings.php to point to your real database.
1. Add the definitions manually to your civicrm.settings.php file as below:

If your civicrm.settings.php does not have this section, copy and paste from here into civicrm.settings.php:

```
// These lines should appear just above the line "Do not change anything below this line. Keep as is"
/**
 * This setting logs all emails to a file. Useful for debugging any mail (or civimail) issues.
 * This will not send any email, so ensure this is commented out in production
 */
// define( 'CIVICRM_MAIL_LOG', '/home/lnp/public_html_test/sites/default/files/civicrm/templates_c/mail.log' );

/**
 * Settings to enable external caching using a Memcache server. This is an
 * advanced features, and you should read and understand the documentation
 * before you turn it on.
 *
 * @see http://civicrm.org/node/126
 */

/**
 * If you have a memcache server configured and want CiviCRM to make use of it,
 * set the following to 1. You should only set this once you have your memcache
 * server up and working, because CiviCRM will not start up if your server is
 * unavailable on the host and port that you specify.
 */
define( 'CIVICRM_USE_MEMCACHE', 0 );

/**
 * Change this to the IP address of your memcache server if it is not on the
 * same machine (Unix).
 */
define( 'CIVICRM_MEMCACHE_HOST', 'localhost' );

/**
 * Change this if you are not using the standard port for memcache (11211)
 */
define( 'CIVICRM_MEMCACHE_PORT', 11211 );

/**
 * Items in cache will expire after the number of seconds specified here.
 * Default value is 3600 (i.e., after an hour)
 */
define( 'CIVICRM_MEMCACHE_TIMEOUT', 3600 );

/**
 * If you are sharing the same memcache instance with more than one CiviCRM
 * database, you will need to set a different value for the following argument
 * so that each copy of CiviCRM will not interfere with other copies. If you only
 * have one copy of CiviCRM, you may leave this set to ''. A good value for
 * this if you have two servers might be 'server1_' for the first server, and
 * 'server2_' for the second server.
 */
define( 'CIVICRM_MEMCACHE_PREFIX', '' );
```

In addition, the following lines need to be added to the very end of the civicrm.settings.php file. The upgrade script will warn you if these lines are not present, and if your file is writable by the web server, the upgrade script will add these lines for you. The simplest thing therefore, is to make sure it's writeable **before** you upgrade.

```
require_once 'CRM/Core/ClassLoader.php';
CRM_Core_ClassLoader::singleton()->register();
```

!!! danger "As of 4.4 CIVICRM_IDS_ENABLE is Migrated to a Permission"

    In previous versions, define( 'CIVICRM_IDS_ENABLE', 0); in the civicrm.settings.php would disable PHPIDS Intrusion Detection. This has now been migrated as a permission based on role and CIVICRM_IDS_ENABLE is ignored.


## Clear Cache

Delete all files in [yoursite]/sites/default/files/civicrm/templates_c/ and clear browser cache

## Run the Upgrade script

**This step is required for ALL upgrades.**

!!! danger "MySQL 4.0 and 4.1 are Not Supported"

    Support for MySQL versions 4.0 and 4.1 has been discontinued. This means that you may encounter various issues and/or unexpected behavior if you attempt to run CiviCRM under these versions, and the CiviCRM engineering team will not provide support for debugging or resolving these issues. You are strongly encouraged to upgrade to the current generally available release of MySQL.


* Point your web browser to the following URL (you should already be logged in to Drupal with administrator-level permissions):

* *- _If "clean URLs" are enabled_
```
http://<your_drupal_home>/civicrm/upgrade?reset=1
```
 *- _If "clean URLs" are NOT enabled_
```
http://<your_drupal_home>/?q=civicrm/upgrade&reset=1
```
* You should see the Upgrade screen.
    * NOTE: This page loads on a drupal maintenance page. If you use a custom maintenance.tpl.php file you may wish to temporarily disable that file and use Drupal's default.
* Should you encounter an error reading: "Version information missing in civicrm database", try running a query like this: 'UPDATE civicrm_domain SET version = '2.2.9' WHERE id = 1;'
* If you are ready to upgrade, click the **Upgrade Now** button.
* You should see the message **Upgrade successful** when the upgrade completes.

    * If you receive any errors during the process, please note down the exact error message, and check for solutions on the [community support forum](http://forum.civicrm.org/).
* Now click the **Return to CiviCRM home page** link.

## Verify and Update Resource URL Settings

If you are running this installation in a different directory from your previous version you may need to update the configured **CiviCRM Resource URL** (Missing icons and images, as well as problems with javascript functions and stylesheets are all symptoms that this setting needs to be updated.)

* Go to **Administer CiviCRM » System Settings » Resource URLs**
* Refer to the field help on that screen for instructions.

## Re-enable compatible CiviCRM integration modules

**Administration » Modules**

Go through your list of CiviCRM integration modules and confirm compatibility with the new release if applicable. (If you've downloaded the module from Drupal.org - check the module's page for compatibility information.)

Re-enable all compatible modules by re-checking the Enabled box. You may want to do this "one at a time" if you're unsure of compatibility.

If you wish to use a different theme for CiviCRM administrative pages than the public pages on your site, enable the **CiviCRM Theme** module that now is included and then set the theme at `admin/appearance`.

## Review and Update Drupal Permissions Settings

* It's a good idea to verify Drupal role-based permissions which were added in recent releases. You can review and update these at **Administer CiviCRM » Users and Permissions » Permissions (Access Control) » Drupal Access Control**:

    * **access CiviMail subscribe/unsubscribe pages** : Enable this permission for the **anonymous user** role if you want any contact in your database to be able to subscribe to public "Mailing List" groups from _http://<drupal_site>/civicrm/mailing/subscribe?reset=1_ AND to unsubscribe by clicking a web-link in your mailings.
    * **view event participants** : If you want to allow some constituents to see a list of registered participants for a given event at _http://<drupal_site>/civicrm/event/participant?id=N&reset=1_, then enable this permission for the appropriate Drupal role(s).
    * **access all custom data** : You must enable this permission for any role which you want to view or edit custom data fields. EXAMPLE: If your site uses Profile(s) which include custom fields - make sure the role(s) that need to access these Profiles have this permission.
    * **access Contact Dashboard** : You can now provide authenticated users with access to a screen where they can review their subscribed groups, contributions, memberships and event registrations (as applicable). Enable this permission for role(s) for which you want to provide this feature.
    * **access CiviEvent** : If you plan to use CiviEvent, enable this permission for role(s) which will be creating and managing Events and Event Participants.
    * **register for events** : If you plan to use CiviEvent and want to allow online event registration - enable this permission. Be sure to assign this permission for the "anonymous" role if you want to allow un-authenticated visitors to register for events.
    * **Delete permissions** - users will not be able to delete contacts, contributions, activities etc unless you specifically enable these permissions

## Clear Views' cache (for sites using Views 3 integration)

* If you were using Views integration prior to this upgrade, you will need to go to **Administration » Structure » Views » Settings » Advanced** and press "Clear Views cache" for Views to capture changes in the CiviCRM Views integration code.

## Restore CiviCRM as the Drupal homepage if appropriate

If you changed the default Drupal homepage from civicrm to node in step 3 above, you need to restore civicrm as the homepage. You can do this by navigating to:
**Administration » Configuration » System » Site Information**
 then change the Default front page to civicrm, and click Save configuration.

!!! warning

    NB: As mentioned in 3 above, there are a variety of modules that may affect the Drupal homepage. Consult the appropriate module's documentation if you are using them to set the homepage to civicrm for the administrative action required to change your site's homepage back to CiviCRM.


## Update System Workflow Message Templates as Needed

If your organization has modified the default versions of System Workflow message templates, then the changes and bug fixes included in an upgrade will need to be merged with your modified versions. ([learn more ...](https://wiki.civicrm.org/confluence/display/CRMDOC/Updating+System+Workflow+Message+Templates+after+Upgrades+-+method+1+-+kdiff))

## Put the Site Online (for production site upgrades)

* Toggle the following feature, and put your site back online:
**Administration » Configuration » Development » Site Maintenance**
* Review Your upgraded site and verify that your data is intact.
* Check out the new features and improvements. A summary of the cool new stuff can be found [here](https://wiki.civicrm.org/confluence/display/CRM/CiviCRM+v3.1).

## Upgrade Trouble-shooting

Check this section for answers to upgrade problems. If your problem isn't addressed here, check out the **[Installation and Configuration Troubleshooting](https://wiki.civicrm.org/confluence/display/CRMDOC/Installation+and+Configuration+Troubleshooting)** page for additional resources.

#### Version information missing in civicrm database

Try running a query like this: 'UPDATE civicrm_domain SET version = '2.2.9' WHERE id = 1;'

Then try invoking the upgrade script again with your browser.

#### Non-recoverable error: Unknown column 'is_deleted' in 'field list', 1054

Another Drupal module is accessing the database and the update process is being blocked. Disable any modules that interact or rely on CiviCRM, such as RealName. Perform the update, then enable those modules again.

#### Page Not Found error

if you get this error when trying to upgrade the database in step 7, you most likely disabled CiviCRM in step 3. Here's how to fix it and get back on track. In short, you have to restore the original version of civicrm, re-enable the module, and then replace the old files with the new.

* Rename the sites/all/modules/civicrm folder to civicrm_new.
* Copy the civicrm folder from your backup into sites/all/modules/
* Go to **Administration » Modules** , check the box next to CiviCRM, and click save.
* Now rename sites/all/modules/civicrm to civicrm_old.
* Rename sites/all/modules/civicrm_new to civicrm.
* Go to Step 7 and try it again. Everything should be back to normal!!
* If so, then don't forget to delete sites/all/modules/civicrm_old.

#### Reset Your User Session

If you are getting foreign key constraint errors when trying to add or modify records, you may need to reset your user session.

* Temporarily enable CiviCRM debug features:
    * Go to **Administer CiviCRM » System Settings » Debugging and Error Handling**
    * Set **Enable Debugging** to Yes and click Save.
* Click the Administer CiviCRM menu (or any other CiviCRM menu item). After the page is loaded, add an additional query string value (sessionReset=2) to the URL in your browser's location bar, and reload the page.
```
// Example URL
http://...../?q=civicrm/admin&sessionReset=2

// Example with Clean URLs
http://...../civicrm/admin?sessionReset=2
```
* Now reset **Enable Debugging** to No and click Save.

!!! danger "Do Not Leave Debug Features Enabled for a Public Site"
 Debugging should be disabled for publicly available sites as it may allow browsers to view system configuration information. |

#### Verify and Update Base Directory and Base URL Settings in the Database

If you are seeing problems with missing images or page styling, you may need to adjust the Base Directory and Base URL Settings in the database. You can do this from:
**Administer CiviCRM » System Settings » Cleanup Caches and Update Paths**

#### Verify and Update Configuration Settings File

CiviCRM in versions 2.2 stores most configuration settings in the database. However values needed to load the code and database are stored in the settings file and may need to be updated prior to beginning the upgrade process if you are upgrading a copy of a working 2.2 site:

**<drupal home>/sites/xxx/civicrm.settings.php (usually** **/sites/default/civicrm.settings.php)**.

* Open civicrm.settings.php with your favorite editor and verify or update the following values:
    * **$civicrm_root** : If you are running this installation in a different directory from your previous version, update this setting with the new path.
    * **CIVICRM_TEMPLATE_COMPILEDIR** : If you are running this installation in a different directory from your previous version, update this setting with the new path.
    * **CIVICRM_UF_BASEURL** : If the Drupal URL for this site is different from your 2.2 site, update this setting with the base URL for your Drupal home page.
    * **CIVICRM_UF_DSN** : If this install will be using a different database for Drupal, update this setting.
    * **CIVICRM_DSN** : This setting must contain the correct information for your current (not yet upgraded) database. If you are upgrading a copy of your database that has a different database name - you will need to change the civicrm_db portion of the setting below. Also review the other values to make sure they are correct for the database that will be used for 4.0
```
define( 'CIVICRM_DSN', 'mysql://crm_db_username:crm_db_password@db_server/civicrm_db?new_link=true');
```
    * **CIVICRM_SITE_KEY** : If you are upgrading to 4.0 from a version earlier than 2.0.7, you will need to define your CIVICRM_SITE_KEY. Components such as CiviMail's cronjob will not function without the site key with CiviCRM 4.0. Learn how to define the site key here: [http://wiki.civicrm.org/confluence/display/CRMDOC/Command-line+Script+Configuration](http://wiki.civicrm.org/confluence/display/CRMDOC/Command-line+Script+Configuration)

#### A fatal error appears when I try to load a CiviCRM page.

```
Fatal error: civicrm_initialize() [CRM:function.require]: Failed opening
required 'CRM/Core/Config.php' (include_path='.:/opt/local/lib/php')
in <drupal root>/modules/civicrm/modules/civicrm.module on line 206
```

This error likely indicates that the new CiviCRM configuration file is not in the expected location or that the $civicrm_root setting is incorrect. See step 3 above.

#### Foreign Key Errors or Warnings During the Database Upgrade

Foreign keys may have been assigned different names on some installations. Also, different versions of MySQL handle the dropping and adding of constraint checks differently. Try [this procedure](https://wiki.civicrm.org/confluence/display/CRMDOC/Ensuring+Schema+Integrity+on+Upgrades) or [this procedure (on the forum)](http://forum.civicrm.org/index.php/topic,4259.msg21599.html#msg21599) to reload your data into a new 4.0 database structure if you are having this type of issue with upgrading your database.

#### Upgrade script fails with fatal database-related errors OR reports "Database check failed"

Download and run [Database Troubleshooting Tools](https://wiki.civicrm.org/confluence/display/CRMDOC/Database+Troubleshooting+Tools) to test the current state of the database and provides a diagnosis. The tools suite also includes a repair facility.

#### Calendar widget doesn't work (throws a Javascript error), and/or CiviMail scripts for open tracking, SOAP authentication, etc. don't function.

If your CiviCRM codebase is NOT located in either **<drupal root>/modules/civicrm** or **<drupal root>/sites/all/modules** (i.e. you are using a symlink from there to your codebase) - you will need to create a local file in the top-level directory of your codebase which points to the location of your drupal sites directory.

```
// Create a new file - settings_location.php
// Enter the following code (substitute the actual location of your
// <drupal root>/sites directory)
<?php
define( 'CIVICRM_CONFDIR', '/home/lobo/public_html/drupal/sites' );
?>
```

#### Access Forbidden (403) Error After Upgrade

This may be caused by directory permission settings. Make sure your <drupal home>/files/civicrm directory is set with chmod a+rwx -R
 If you're still getting this error and have clean URLs enabled, try disabling clean URLS (Drupal >> admin >> settings) prior to running the session reset and cleanup. Then re-enable clean URLs.

#### Screen grays out and a camera icon appears whenever you click any link in CiviCRM

This error is caused by a known problem in the [Drupal Lightbox2 module](http://drupal.org/project/lightbox2). Either disable the module in **Administration » Modules** or upgrade to the dev version of the Lightbox2 (dated July 2009 or later) that fixes this issue.

#### Reset config_backend

Having strange problems with Administrative settings forms after upgrade? Try deleting all files in [yoursite]/sites/default/files/civicrm/templates_c/ and sites/default/files/civicrm/ConfigAndLog/Config.IDS.ini

#### UpdateMembershipRecord.php won't run after upgrading from prior versions

Double check that there is a Membership Status Rule called "Deceased" and that it is active in **Administer CiviCRM » CiviMember**  **» Membership Status Rules.** The solution is described in this [forum thread](http://forum.civicrm.org/index.php?topic=10092).

#### The menus are wrong, the admin dashboard does not have the correct links and settings, or get an error with CRM_Core_Invoke::require_once()

You can re-build the menu/admin dashboard by visiting the following url - ([http:///civicrm/menu/rebuild?reset=1](http://wiki.civicrm.org/civicrm/menu/rebuild?reset=1) (http://%3cdomain%3e/civicrm/menu/rebuild?reset=1))

#### Version information missing in civicrm database error

Try running a query like this: 'UPDATE civicrm_domain SET version = '2.2.9' WHERE id = 1;'

Then try invoking the upgrade script again with your browser.

#### **Upgrade script failed to modify civicrm.settings.php**

If you relied on the upgrade script to modify civicrm.settings.php, check that it did so. If it didn't, restart the upgrade, and modify the file by hand.

#### **Rolling back to previous/backed-up version of CiviCRM**

Sometimes an upgrade fails and you need to get back to the last working version. You should have backups of the database saved as well as a backup of the civicrm files from the /sites/all/modules/ directory. Here are the steps you need to take in order to roll back to the last working version before your upgrade:

1. Go into maintenance mode and take the site offline.
1. In the modules screen, disable all the CiviCRM modules EXCEPT the Core CiviCRM module. Make sure to take note of which CiviCRM modules you've disabled so you can enable them again after you restore the backups.
1. Make sure to drop all the tables in the civicrm database that relate to civicrm. If you're sharing a database with Drupal, make sure to ONLY drop the tables that belong to CiviCRM and NOT the tables for Drupal. If Drupal's tables are in a separate database, then you should be safe as long as you don't touch any of the Drupal tables.
1. Restore all the civicrm tables from your database backup back into the database.
1. In the /sites/all/modules directory, make sure to delete the entire civicrm directory ("../sites/all/modules/civicrm").
1. Restore the civicrm directory in /sites/all/modules/ from your files backup.
1. CRUCIAL: Remove the "templates_c" directory from ../sites/default/files/civicrm/ (don't worry, CiviCRM will recreate these files when you reload the pages).
1. Go back to Drupal's Modules screen and enable the CiviCRM modules you disabled in step 2.
1. Bring the site back online by turning off Maintenance mode.
1. Load up civicrm by going to [http://yoursitename.com/civicrm](http://yoursitename.com/civicrm) (or wherever you have civicrm installed). This should have restored you back to the last working condition.

Sometimes it helps to rebuild the menu/admin dashboard by visiting the url [http://yoursitename.com/civicrm/menu/rebuild?reset=1](http://yoursitename.com/civicrm/menu/rebuild?reset=1)

#### After clicking "Upgrade Now", upgrade freezes with "[]" message (Firefox)

This may be a compatibility issue with certain releases of Firefox. The issue has appeared intermittently and has not been reliably reproduced. If you encounter it, please report details at [CRM-11386](http://issues.civicrm.org/jira/browse/CRM-11386); to continue with the upgrade, you can work-around the issue by upgrading Firefox or using a different web browser.