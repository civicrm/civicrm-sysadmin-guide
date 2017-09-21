# Cleanup Caches and Update Paths

## Overview

On this settings page you can:

* Clear the cache
* View and update the value that CiviCRM has determined for your Base Url and site name.

[![](https://wiki.civicrm.org/confluence/download/attachments/142573577/Settings_-_Cleanup_Caches_and_Update_Paths_4.4.png?version=1&modificationDate=1401984657000&api=v2)](https://wiki.civicrm.org/confluence/download/attachments/142573577/Settings_-_Cleanup_Caches_and_Update_Paths_4.4.png?version=1&modificationDate=1401984657000&api=v2)

Although you enter your Base URL in civicrm.settings.php the value shown here reflects additional attempts by CiviCRM to automatically detect the correct base url in various environments - for example in multilingual sites.

## Troubleshooting - Known issues and workarounds

Sometimes CiviCRM's attempt to detect the Base Url can be confused by various factors in the site environment such as http redirects, CMS multilingual settings and other CMS modules.

### Drupal Modules

#### Internationalization (CiviCRM 4.4 and lower)

If Drupal Language detection is set to "By URL" and the "Language Domain" setting has the protocol entered in the URL (Eg. http or https) then the CiviCRM Base URL detection includes the protocol twice. See [Jira Issue CRM-14489](https://issues.civicrm.org/jira/browse/CRM-14489).

#### Domain Access

You may need a bit of code in civicrm.settings.php to get the correct CIVICRM_UF_BASEURL to work on front end pages exposed on various domains. See [Forum Post on Domain Access and Drupal](http://forum.civicrm.org/index.php/topic,24657.msg104529.html#msg104529).

# Installation and Configuration Troubleshooting

!!! tip "Troubleshooting Resources"

    For installations problems, first make sure that you're read and followed the step-by-step directions for your type of installation: Drupal Installation Guide, Joomla Installation Guide, or Standalone Installation Guide.

     You can often find solutions to your issue by searching the **[installation support section of the community forum](http://forum.civicrm.org/index.php/board,6.0.html)** OR the [community mailing list archives](http://www.nabble.com/CiviCRM-Community-Mailing-List-Archives-f15986.html). You can also check out the **Installation section of our FAQs**.

     If you don't find an answer to your problem in those places, the next step is to **[post a support request on the forum](http://forum.civicrm.org/index.php/board,6.0.html)**.


### "DB_DataObject Error: DB Error: connect failed"

If you get this error on a new Drupal/CiviCRM installation, you may have skipped the step of running CiviCRM's installation script. You can not install CiviCRM using the standard Drupal module installer. Review the [installation instructions here](https://wiki.civicrm.org/confluence/display/CRMDOC/Installing+CiviCRM+for+Drupal+6).

### "Could not find valid Key"

If you are getting this error when submitting a form (search or adding / editing records), check the following:

* Ensure cookies are enabled on browser. Like most web applications, CiviCRM can not function properly with cookies disabled.
* Ensure your configuration settings are using the same "machine name" for CiviCRM and your CMS (Drupal or Joomla!). For example, you will have problems if CiviCRM is configured to use [http://example.com](http://example.com) as it's BASE_URL and you CMS is using [http://www.example.com](http://www.example.com). You can use an .htaccess entry to redirect to the configured URL if needed (i.e. push all users to [http://www.example.com](http://www.example.com) even if they hit [http://example.com](http://example.com)).
  1. For Drupal sites, ensure that uid = 0 exists in your Drupal users table. This is required for anonymous access to CiviCRM pages and forms to work properly.
  1. For Drupal sites, ensure that Drupal sessions table uses UTF8 collation, AND that the session column in this table is of SQL type longtext in the schema. You can check both of these in phpMyAdmin, or issue these commands from mysql command line:
```
$ desc session;
$ show create table sessions;
```

### "Failed to initialize storage module: user" fatal error message (new installs)

If you see this error, you may need to modify the session.save_handler method for your site. Check with your hosting provider for the recommended way to do this. [More info at this forum post](http://forum.civicrm.org/index.php/topic,8561.0/topicseen.html).

### Using the Built-in Debugging Tools

CiviCRM provides several URL-param debug settings which can help debug and resolve problems such as corrupt sessions and obsolete template caches. In order to use these URL parameters, you must first enable debugging from **Administer » System Settings » Debugging and Error Handling**.

!!! danger "Do NOT Leave Debug Turned On in Production Sites"

    It is critical that the DEBUG features are disabled in production sites. When debug is enabled - system paths and other internal settings may be exposed to browsers.


#### Debug Commands

* **Smarty Debug Window** - Loads all variables available to the current page template into a pop-up
 window. To trigger, add '&smartyDebug=1' to any CiviCRM URL query string.

* **Session Reset** - Resets all values in your client session. To trigger, add '&sessionReset=2'

* **Directory Cleanup** - Empties template cache and/or upload file folders.
    * To empty template cache (civicrm/templates_c folder), add '&directoryCleanup=1'
    * To remove temporary upload files (civicrm/upload folder), add '&directoryCleanup=2'
    * To cleanup both, add '&directoryCleanup=3'

* **Stack Trace** - To display stack trace at the top of the page when an error occurs, set Enable Backtrace from **Administer » System Settings » Debugging**  **and Error Handling**.

### Resolving RELAY-DENIED Errors When Trying to Send Emails to Contacts

(under construction)

### Configuring PHP to Handle Import Files from/on Macintosh Computers

In order for CiviCRM to properly handle CSV import files created on Macs, you may need to update a PHP setting for detecting line endings. The symptom of this problem is that the "Import Contacts: Match Fields" step shows the file as one big record - rather than recognizing the end of each row.

* Open /opt/local/etc/php.ini in your favorite editor (you may need root privileges to do this).
* Look for the following setting in the Fopen wrappers section (near line 525):

```
auto_detect_line_endings = On
```

* If this line is commented out, or set to Off, change the value to On as shown above.
* Stop and start your local instance of Apache

```
$ sudo /opt/local/etc/rc.d/apache2.sh stop
$ sudo /opt/local/etc/rc.d/apache2.sh start
```

### Database version inconsistencies / upgrade problems

Download and run [Database Troubleshooting Tools](https://wiki.civicrm.org/confluence/display/CRMDOC/Database+Troubleshooting+Tools) to test the current state of the database and provides a diagnosis. The tools suite also includes a repair facility.

### Windows XP SP3: Problem setting the /sites/default/files directory permissions so it is writable

There is a weird glitch that can happen under Windows XP (SP3) (and perhaps other Windows OS versions): if you try to change the permissions for a file or directory, they won't 'stick.' For example, if you go to your <yoursite>/sites/default/files directory and right click on it and bring up the Properties dialog, you can see the permissions for the directory. If this is set to read only, you will get an error message when you install CiviCRM telling that it needs to be writable. If you click on the "Read Only" checkbox so that it is **_not checked_** and then OK your change, the permission should be changed. (The directory should now be writable.) However, sometimes this change will not actually take effect. You have to make the change using the _ATTRIB_ command in a command prompt window. Open a command prompt window and go to the <yoursite>/sites/default directory. Type "help attrib" to get help using the attrib command and then enter the appropriate arguments to set the /files directory permissions (attributes) so that it's writeable. This will usually invovle the "-R" attribute and the "/D" argument.

Installing 4.1.2 on windows/drupal will not work with ATTRIB only, but needs hack. It's been proposed to install first 4.0.8 and then upgrade. You can also hack /modules/civicrm/install/index.php on line 368 replacing it with _$this->requireWriteable($dir,_ Follow Issue tracker: [CRM-10175](http://issues.civicrm.org/jira/browse/CRM-10175)

### civicrm_strip_non_numeric does not exist error

This error occurs when your database has lost a MySQL function definition for civicrm_strip_non_numeric. It can happen when moving from one server to another with the wrong mysqldump parameters, or if you use phpMyAdmin or certain other tools to rename a database, since they copy most but not all of the contents of the database to a database with the new name before deleting the old one.

The solution is to navigate to the following url:

http://<yourdomain>/civicrm/menu/rebuild?reset=1&triggerRebuild=1

Note the &triggerRebuild=1 key-value pair. It rebuilds the triggers and this routine.

### Firewall with NAT

If CiviCRM is installed on a server behind a firewall with NAT, you'll need to add your **internal** IP address and host name to /etc/hosts (on Linux). Otherwise, the dashboard will time out and the Force Secure URLs option will have issues. The reasoning behind this is, the dashboard tries to connect to http(s)://www.SITE.com to load the dashboard, but times out since SITE.com is being resolved via the public IP address. Your server can't access this public IP address if it's behind a firewall with NAT.# Ensuring Schema Integrity on Upgrades

### Introduction

When a CiviCRM site is upgraded to a new version, there are frequently changes to the structure of the database. This may include adding or dropping tables, columns, indexes and foreign keys. Generally, the SQL upgrade script provided with each new version does a decent job of modifying the existing database to match the new schema.

However, there are occasional issues during the upgrade which prevent a full implementation of the new schema. These issues generally show up as error messages or warnings during the upgrade.At best, these differences don't have any impact on your use of the database. In other cases, it might result in your database not running as quickly as it can. In the worse case, it can lead to data loss when merging records.

We recommend you check and fix your schema if you've experienced any unexpected database errors or warning during an upgrade. We also recommend following these steps if your database as been through several version upgrades.

The [Civi Schema Harmonizer](https://github.com/progressivetech/civi-schema-harmonizer) is a project designed to help you automate the process of fixing your database schema. It provides the option of just fixing foreign keys (a less destructive approach that will solve the most critical problems) as well as the option to fix all CiviCRM tables in your database.

The procedures below provide a way to manually make these changes.

After running these steps, you can be confident that your database schema matches the current production version exactly, with all indexes, foreign keys, defaults and other constraints.

### Procedure to rebuild schema for database versions 2.2.x or later

1. Take a DATA dump of existing, intact database. If possible, do not dump data from a database upon which you have attempted an upgrade and failed. Dump data from a database backup you did prior to upgrading that has a schema version number that is accurate. Find this information in _civicrm_domain.version_. A value in this column containing the word 'upgrade' indicates a database that failed to upgrade and may be stuck 'between schemas'. Lets call this dump file datafile.sql
```
mysqldump -u username -p -c -e -n -t --skip-triggers databasename > datafile.sql
```
**Note:** You may wish to exclude the following tables from your data-only export: any table with the word "cache" in it or any table starting with "civicrm_import_job"
1. Take a STRUCTURE only dump for all your custom data tables (civicrm_value_* tables or custom_value_* tables) of your database.
```
mysqldump -d -R -u username -p databasename civicrm_value_blah_1 civicrm_value_blah_2 > customfieldsfile.sql
```
 The only option that is different than creating an entire backup is the -d switch, which tells mysqldump not to output the data. List all the relevant custom value tables before the > customfieldsfile.sql. If you did not exclude "civicrm_import_job*" tables in step 1, you must export these tables' data and structure in step 2 along with your custom data tables.

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
1. Rebuild the triggers by running
```
http://<your_site>/civicrm/menu/rebuild?reset=1&triggerRebuild=1
```
1. Make sure you have the backup of your original working database and replace this with the new database created in step 3.

Browse through few civicrm pages to verify if civicrm is working fine along with all the custom data. Also make a few checks for e.g if number of contacts / custom-data are same in both the databases.

Now the new database is ready for upgrade to next higher version.

### How to Deal With A Partially Upgraded Database When You Don't Have a Backup

Recently I was asked to help in a situtation where there **was no pre-upgrade** backup. This is a worst case scenario. In this example, the database was previously 3.0 and while attempting an upgrade to 3.1 it failed, with no backup to fall back on. When this happens, CiviCRM will not allow you to re-run the upgrade script because the database integrity check will fail. Attempting to circumvent this integrity check almost always results in worse errors.

The only real option is to compare a "clean and correct schema" to the partially-upgraded and fouled schema that exists on your server, and manually change the fouled schema to be what it should. This is a painstaking process.

Here's how I chose to deal with it. Remember: the goal is to compare this clean schema to your partially-upgraded, fouled schema that exists on your server.

1. Look in the .tar.gz (or .zip) distributable of the prior version - the one you were trying to upgrade from. In the "sql" folder you will find civicrm.mysql, which is the "clean schema" of that version, structure only - no data.
1. Find your partially-upgraded database and export the structure only. For this purpose I like using [PHPMyAdmin](http://www.phpmyadmin.net/home_page/index.php), which is available already on most servers. If it is not on your server, you can install - it is open source. Export the "structure" only (see the checkbox on the 'export' tab) and save it to your local computer, during the export, omit all tables that begin with 'civicrm_value' (these are custom fields) as well as 'civicrm_import', which are not relevant to this task
1. Take the civicrm.mysql clean schema and import it into a new test database on your server, then immediately export it using PHPMyAdin, just as you did with the fouled schema. The purpose of this action is to get the formatting of the CREATE statements to be as close to identical as possible.
1. Now, using a diff utility such as 'diff' with [Unxitils](http://unxutils.sourceforge.net/), 'windiff' or [WinMerge](http://winmerge.org/) compare the two schemas side by side.
1. You will notice, most likely, several things:

* Tables existing in the fouled schema that do not exist in the clean schema
* Some fields that exist in the fouled schema that do not exist in the clean one
* Minor differences in comments, or grammar, or /r /n code (newlines)
* Some identically named fields existing in a slightly different order
* Many ALTER statements with foreign key stuff

1. You can ignore all but the first two listed above.
1. Make a backup of the fouled database
1. Take great care to drop all tables that exist in the fouled schema that do not in the clean one. Remember, **don't** drop your 'civicrm_import' tables nor your 'civicrm_value' tables. These are custom fields and import records - keep these tables.
1. Remove any fields that existed in the fouled schema but do not in the clean one
1. In table "civicrm_domain" set "version" to the previous version (before the upgrade)
1. Attempt the upgrade again