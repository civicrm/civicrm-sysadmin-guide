# Troubleshooting


## General advice

If you have any problems with these procedures, try searching the [community forums](http://forum.civicrm.org/) and this wiki for solutions. If you've gotten an error message, use that message in your search. If you can not resolve the problem, then post your problem to the forum. Be sure to include the CiviCRM version and revision you are upgrading FROM and TO; your Joomla version; your PHP and MySQL versions; the steps you've taken and a the exact error message or problem that resulted.

For installations problems, first make sure that you're read and followed the step-by-step directions for your type of installation: Drupal Installation Guide, Joomla Installation Guide, or Standalone Installation Guide.

You can often find solutions to your issue by searching the [installation support section of the community forum](http://forum.civicrm.org/index.php/board,6.0.html) OR the [community mailing list archives](http://www.nabble.com/CiviCRM-Community-Mailing-List-Archives-f15986.html). You can also check out the Installation section of our FAQs.

If you don't find an answer to your problem in those places, the next step is to [post a support request on the forum](http://forum.civicrm.org/index.php/board,6.0.html).






## Symptoms

### Access Forbidden (403) Error After Upgrade

This may be caused by directory permission settings.

Make sure your `<drupal_root>/files/civicrm` directory is set with
 
```bash
$ chmod a+rwx -R
```

If you're still getting this error and have clean URLs enabled, try disabling clean URLS (Drupal >> admin >> settings) prior to running the session reset and cleanup. Then re-enable clean URLs.

### Calendar widget throws a Javascript error

If applicable, try [creating a config file pointing to your Drupal sites directory](#conf-drupal-sites).

### CiviMail is unable to track clicks and opens

If applicable, try [creating a config file pointing to your Drupal sites directory](#conf-drupal-sites).

### "civicrm_strip_non_numeric does not exist"

This error occurs when your database has lost a MySQL function definition for `civicrm_strip_non_numeric`. It can happen when moving from one server to another with the wrong `mysqldump` parameters, or if you use phpMyAdmin or certain other tools to rename a database, since they copy most but not all of the contents of the database to a database with the new name before deleting the old one. [Re-build your database triggers](#trigger-rebuild) to fix the problem.

### "Could not find valid Key"

If you are getting this error when submitting a form (search or adding / editing records), check the following:

* Ensure cookies are enabled on browser. Like most web applications, CiviCRM can not function properly with cookies disabled.
* Ensure your configuration settings are using the same "machine name" for CiviCRM and your CMS. For example, you will have problems if CiviCRM is configured to use `http://example.com` as its `BASE_URL` and your CMS is using `http://www.example.com`. You can use an `.htaccess` entry to redirect to the configured URL if needed (i.e. push all users to `http://www.example.com` even if they hit `http://example.com`).

    1. For Drupal sites, ensure that `uid = 0` exists in your Drupal users table. This is required for anonymous access to CiviCRM pages and forms to work properly.
    1. For Drupal sites, ensure that Drupal sessions table uses UTF8 collation, AND that the session column in this table is of SQL type longtext in the schema. You can check both of these in phpMyAdmin, or issue these commands from mysql command line:
    
        ```
        > desc session;
        > show create table sessions;
        ```

### "DB_DataObject Error: DB Error: connect failed"

If you get this error on a new Drupal/CiviCRM installation, you may have skipped the step of running CiviCRM's installation script. You can not install CiviCRM using the standard Drupal module installer.

### "Failed to initialize storage module: user" fatal error message (new installs)

If you see this error, you may need to modify the session.save_handler method for your site. Check with your hosting provider for the recommended way to do this. [More info at this forum post](http://forum.civicrm.org/index.php/topic,8561.0/topicseen.html).

### Foreign key constraint errors

If you are getting foreign key constraint errors when trying to add or modify records, you may need to [reset your session](#reset-session).

### Foreign Key Errors or Warnings During the Database Upgrade

Foreign keys may have been assigned different names on some installations. Also, different versions of MySQL handle the dropping and adding of constraint checks differently. Try [this procedure](https://wiki.civicrm.org/confluence/display/CRMDOC/Ensuring+Schema+Integrity+on+Upgrades) or [this procedure (on the forum)](http://forum.civicrm.org/index.php/topic,4259.msg21599.html#msg21599) to reload your data into a new 4.0 database structure if you are having this type of issue with upgrading your database.

### Missing images or page style

If you are seeing problems with missing images or page styling, you may need to [update your base directory and URL settings](#base-settings).

### Out of Date Joomla Version

> Joomla 2.5 reached _End of Life_ (EOL) in 2014 and is no longer supported by the Joomla! Project.

While CiviCRM 4.7 supports Joomla 2.5.x or 3.x.x, it is strongly recommended by the Joomla project that 2.5 users upgrade to the most current version of 3.x ([https://docs.joomla.org/Joomla!_CMS_versions](https://docs.joomla.org/Joomla!_CMS_versions)).

### Screen grays out and a camera icon appears whenever you click any link in CiviCRM

This error is caused by a known problem in the [Drupal Lightbox2 module](http://drupal.org/project/lightbox2). Either disable the module in **Administration » Modules** or upgrade to the dev version of the Lightbox2 (dated July 2009 or later) that fixes this issue.

### "Unknown column 'is_deleted' in 'field list'"

If you are getting this error while upgrading CiviCRM in Drupal, it's possible that another Drupal module is accessing the database and the update process is being blocked. Disable any modules that interact or rely on CiviCRM, such as RealName. Perform the update, then enable those modules again.

### UpdateMembershipRecord.php won't run after upgrading from prior versions

Double check that there is a Membership Status Rule called "Deceased" and that it is active in **Administer CiviCRM » CiviMember**  **» Membership Status Rules.** The solution is described in this [forum thread](http://forum.civicrm.org/index.php?topic=10092).

### Upgrade script failed to modify civicrm.settings.php

If you relied on the upgrade script to modify `civicrm.settings.php`, check that it did so. If it didn't, restart the upgrade, and modify the file by hand.

### Version information missing in CiviCRM database

Try running a query like this:

```sql
UPDATE civicrm_domain SET version = 'x.x.x' WHERE id = 1;
```

_(where `x.x.x` should be replaced with the version you're upgrading to)_

Then try invoking the upgrade script again with your browser.

### WordPress menus are wrong

Try [rebuilding the WordPress menus](#rebuild-wp-menus).

### "You do not have sufficient permissions to access this page."

If you get this error when trying to run the upgrade script in WordPress, you most likely deactivated CiviCRM plugin during the upgrade process. You just have to visit `<wordpress_home>/wp-admin/plugins.php` and activate the plugin, and then proceed.

### "Your PHP version is missing zip functionality"

If you get this error when installing or upgrading CiviCRM for Joomla, download and use the `civicrm-4.7.x-joomla-alt.zip` package.









## Solutions

### Cleanup Caches and Update Paths {:#clear-cache}

On this settings page you can:

* Clear the cache
* View and update the value that CiviCRM has determined for your Base Url and site name.

[![](https://wiki.civicrm.org/confluence/download/attachments/142573577/Settings_-_Cleanup_Caches_and_Update_Paths_4.4.png?version=1&modificationDate=1401984657000&api=v2)](https://wiki.civicrm.org/confluence/download/attachments/142573577/Settings_-_Cleanup_Caches_and_Update_Paths_4.4.png?version=1&modificationDate=1401984657000&api=v2)

Although you enter your Base URL in civicrm.settings.php the value shown here reflects additional attempts by CiviCRM to automatically detect the correct base url in various environments - for example in multilingual sites.

### Create a config file pointing to your Drupal sites directory {:#conf-drupal-sites}

If your CiviCRM codebase is not located in either `<drupal_root>/modules/civicrm` or `<drupal_root>/sites/all/modules` (for example, if you're using a symlink from there to your codebase) - you will need to create a local file in the top-level directory of your codebase which points to the location of your drupal sites directory.

```
// Create a new file - settings_location.php
// Enter the following code (substitute the actual location of your
// <drupal root>/sites directory)
<?php
define( 'CIVICRM_CONFDIR', '/home/lobo/public_html/drupal/sites' );
?>
```

### Fix problems with your database schema

When a CiviCRM site is upgraded to a new version, there are frequently changes to the structure of the database. This may include adding or dropping tables, columns, indexes and foreign keys. Generally, the SQL upgrade script provided with each new version does a decent job of modifying the existing database to match the new schema.

However, there are occasional issues during the upgrade which prevent a full implementation of the new schema. These issues generally show up as error messages or warnings during the upgrade.At best, these differences don't have any impact on your use of the database. In other cases, it might result in your database not running as quickly as it can. In the worse case, it can lead to data loss when merging records.

We recommend you check and fix your schema if you've experienced any unexpected database errors or warning during an upgrade. We also recommend following these steps if your database as been through several version upgrades.

The [Civi Schema Harmonizer](https://github.com/progressivetech/civi-schema-harmonizer) is a project designed to help you automate the process of fixing your database schema. It provides the option of just fixing foreign keys (a less destructive approach that will solve the most critical problems) as well as the option to fix all CiviCRM tables in your database.

The procedures below provide a way to manually make these changes.

After running these steps, you can be confident that your database schema matches the current production version exactly, with all indexes, foreign keys, defaults and other constraints.

1. Take a DATA dump of existing, intact database. If possible, do not dump data from a database upon which you have attempted an upgrade and failed. Dump data from a database backup you did prior to upgrading that has a schema version number that is accurate. Find this information in _civicrm_domain.version_. A value in this column containing the word 'upgrade' indicates a database that failed to upgrade and may be stuck 'between schemas'. Lets call this dump file datafile.sql

    ```
    mysqldump -u username -p -c -e -n -t --skip-triggers databasename > datafile.sql
    ```
    
    **Note:** You may wish to exclude the following tables from your data-only export: any table with the word "cache" in it or any table starting with "civicrm_import_job"

1. Take a STRUCTURE only dump for all your custom data tables (civicrm_value_* tables or custom_value_* tables) of your database.

    ```
    mysqldump -d -R -u username -p databasename civicrm_value_blah_1 civicrm_value_blah_2 > customfieldsfile.sql
    ```
    
     The only option that is different than creating an entire backup is the -d switch, which tells mysqldump not to output the data. List all the relevant custom value tables before the > customfieldsfile.sql. If you did not exclude "civicrm_import_job*" tables previously, you must export these tables' data and structure along with your custom data tables.

1. Create a new database for your rebuilt data.
1. Import the original database structure from the same version of CiviCRM you are currently using: civicrm/sql/civicrm.mysql (this file is found relative to your root civicrm directory)
    ```
    mysql -u username -p newdatabase < civicrm/sql/civicrm.mysql
    ```
    
1. Now import customfieldsfile.sql (taken from step2) in the new database.
    ```
    mysql -u username -p newdatabase < customfieldsfile.sql
    ```
    
1. Now import your datafile.sql (taken from step1) again in the new database.

    ```
    mysql -u username -p newdatabase < datafile.sql
    ```
    
    **A note on possible Foreign Key Constraint Violation errors during import** (and what to do about them): You may receive Foreign Key constraint violation errors on import because of the order in which the tables are exported and then imported, which is alphabetical. For example, data for the civicrm_activity_assignment table would be imported before data for the civicrm_contact table, violating the Foreign Key constraint defined on the civicrm_activity_assignment.assignee_contact_id field. You can avoid this by instructing MySQL to disable Foreign Key checks before the import and to re-enable it when the import is complete. Before importing the datafile.sql file, open it in a text editor and add the following MySQL code to the first line of the file:

    ```
    SET foreign_key_checks = 0;
    ```
    
    And append the following code to the last line of the file:
     
    ```
    SET foreign_key_checks = 1;
    ```
    
1. [Rebuild your database triggers](#trigger-rebuild) 
    
1. Make sure you have the backup of your original working database and replace this with the new database created above.

Browse through few civicrm pages to verify if civicrm is working fine along with all the custom data. Also make a few checks for e.g if number of contacts / custom-data are same in both the databases.

Now the new database is ready for upgrade to next higher version.

### Make CiviCRM available behind a NAT firewall {:#nat-firewall}

If CiviCRM is installed on a server behind a firewall with NAT, you'll need to add your **internal** IP address and host name to /etc/hosts (on Linux). Otherwise, the dashboard will time out and the Force Secure URLs option will have issues. The reasoning behind this is, the dashboard tries to connect to http(s)://www.SITE.com to load the dashboard, but times out since SITE.com is being resolved via the public IP address. Your server can't access this public IP address if it's behind a firewall with NAT.

### Rebuild CiviCRM menus {:#rebuild-civicrm-menus}

Sometimes it helps to rebuild the menu/admin dashboard by visiting the following URL:

`http://<yourdomain>.org/civicrm/menu/rebuild?reset=1`

### Rebuild database triggers {:#trigger-rebuild}

If you need to rebuild your database triggers, navigate to the following url:

`http://<yourdomain>.org/civicrm/menu/rebuild?reset=1&triggerRebuild=1`

Note the `&triggerRebuild=1` key-value pair.

### Rebuild WordPress menus {:#rebuild-wp-menus}

You can re-build the menu/admin dashboard by visiting the following url

`http://<yourdomain>.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/menu/rebuild&reset=1`

### Reset config_backend

Having strange problems with Administrative settings forms after upgrade? Try deleting all files in the following directories:

* `sites/default/files/civicrm/templates_c/` 
* `sites/default/files/civicrm/ConfigAndLog/Config.IDS.ini`

### Reset Your User Session {:#reset-session} 

To reset your user session:

1. Temporarily enable CiviCRM debug features:
    * Go to **Administer CiviCRM » System Settings » Debugging and Error Handling**
    * Set **Enable Debugging** to Yes and click Save.
1. Click the Administer CiviCRM menu (or any other CiviCRM menu item). After the page is loaded, add an additional query string value (`sessionReset=2`) to the URL in your browser's location bar, and reload the page.
    ```
    http://...../civicrm/admin?sessionReset=2
    ```
    
1. Now reset **Enable Debugging** to No and click Save.

!!! danger "Do Not Leave Debug Features Enabled for a Public Site"
    Debugging should be disabled for publicly available sites as it may allow browsers to view system configuration information.
    
### Roll back to previous version of CiviCRM (from a backup)

Sometimes an upgrade fails and you need to get back to the last working version. You should have backups of the database saved as well as a backup of the civicrm files from the `/sites/all/modules/` directory. Here are the steps you need to take in order to roll back to the last working version before your upgrade:

1. Go into maintenance mode and take the site offline.
1. In the modules screen, disable all the CiviCRM modules EXCEPT the Core CiviCRM module. Make sure to take note of which CiviCRM modules you've disabled so you can enable them again after you restore the backups.
1. Make sure to drop all the tables in the civicrm database that relate to civicrm. If you're sharing a database with Drupal, make sure to ONLY drop the tables that belong to CiviCRM and NOT the tables for Drupal. If Drupal's tables are in a separate database, then you should be safe as long as you don't touch any of the Drupal tables.
1. Restore all the civicrm tables from your database backup back into the database.
1. In the `/sites/all/modules` directory, make sure to delete the entire civicrm directory (`../sites/all/modules/civicrm`).
1. Restore the civicrm directory in `/sites/all/modules/` from your files backup.
1. CRUCIAL: Remove the `templates_c` directory from `../sites/default/files/civicrm/` (don't worry, CiviCRM will recreate these files when you reload the pages).
1. Go back to Drupal's Modules screen and enable the CiviCRM modules you disabled.
1. Bring the site back online by turning off Maintenance mode.
1. Load up civicrm by going to `http://yoursitename.com/civicrm` (or wherever you have civicrm installed). This should have restored you back to the last working condition.

Sometimes it helps to [rebuild the CiviCRM menus](#rebuild-civicrm-menus).

### Update Base Directory and Base URL Settings {:#base-settings}

To update your Base Directory and Base URL Settings in the database, visit this settings page:

* **Administer CiviCRM > System Settings > Cleanup Caches and Update Paths**

### Using the Built-in Debugging Tools {:#debugging}

CiviCRM provides several URL-param debug settings which can help debug and resolve problems such as corrupt sessions and obsolete template caches. In order to use these URL parameters, you must first enable debugging from **Administer » System Settings » Debugging and Error Handling**.

!!! danger "Do NOT Leave Debug Turned On in Production Sites"
    It is critical that the DEBUG features are disabled in production sites. When debug is enabled - system paths and other internal settings may be exposed to browsers.

* **Smarty Debug Window** - Loads all variables available to the current page template into a pop-up
 window. To trigger, add '&smartyDebug=1' to any CiviCRM URL query string.

* **Session Reset** - Resets all values in your client session. To trigger, add '&sessionReset=2'

* **Directory Cleanup** - Empties template cache and/or upload file folders.
    * To empty template cache (civicrm/templates_c folder), add '&directoryCleanup=1'
    * To remove temporary upload files (civicrm/upload folder), add '&directoryCleanup=2'
    * To cleanup both, add '&directoryCleanup=3'

* **Stack Trace** - To display stack trace at the top of the page when an error occurs, set Enable Backtrace from **Administer » System Settings » Debugging**  **and Error Handling**.
