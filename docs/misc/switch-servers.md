# Moving an Existing Installation to a New Server or Location

Some settings in the CiviCRM database relate to a physical location, such as a server file path or a public URL. These settings are usually stored as relative paths (with respect to the default file container, eg `sites/default/files/civicrm` or `media/civicrm`), but sometimes an admin customizes them to use an absolute path. If you have any absolute paths, then you must usually change these when moving an existing CiviCRM installation to a new server. This documentation covers the general steps.

**If you regularly copy a production database to a test server, you can [use a settings override](/customize/settings.md) to avoid having to "fix" the directory and URL-based settings each time you copy the database over.**

_Older versions of CiviCRM included a script which performed a heuristic search-replace against file paths and URLs. The script had various interfaces (e.g. `doSiteMove()`, `civicrm/admin/setting/updateConfigBackend`, `bin/migrate/move.php`, `drush civicrm-update-cfg`, `wp civicrm update-cfg`.) But it was not maintained well and was removed in 4.7. Best advice: stick to relative paths under the default file container, or use a settings-override for absolute paths. (See also: [forum discussion](https://forum.civicrm.org/index.php?topic=36692.msg155904#msg155904))_

These instructions have been rewritten based on my recent migration experience and are also informed by this blog post: [http://civicrm.org/blogs/eileen/drush-bam-civi-wham](http://civicrm.org/blogs/eileen/drush-bam-civi-wham). The overall process I am suggesting is to install a fresh working copy of CiviCRM on your new server. Next copy any custom code then move the majority of your database from the old server, ignoring domain and cache tables. This leaves the good working domain data in place on your new server.

Some sites may have been advised to create a file `/path/to/civicrm/settings_location.php`, which hardcodes the path to the site configuration directory. This is used to work around issues with deducing the site configuration path. If your site has such a file, it may require updating along with the hardcoded paths in `civicrm.settings.php`

If you are using Joomla, Akeeba backup is a handy plugin to create complete backups of your site, including CiviCRM. It does not only copy your database but all your files too. Configure Akeeba to ignore tables, table content and files as described below (Note, sometimes it is best to NOT ignore the civicrm_domain table. If you get an error on your copy when go to access the Civi pages, try NOT ignoring that table in your Akeeba backup). Such a backup can be restored easily and completely using its complementary Kickstart script on any site, not just your original site, because it contains all restoration scripts necessary to get you up and running.
 If you are transferring to a new host some paths and URLs probably have changed so you must update:

1. all URLs that points to your old site in both the civicrm.settings.php (in .../administrator/components/com_civicrm and .../components/com_civicrm) as described below
1. all database connection strings in both the civicrm.settings.php (in .../administrator/components/com_civicrm and .../components/com_civicrm) as described below
1. all file paths to the installation in both the civicrm.settings.php files *as well as* the civicrm.config.php file which resides in <joomla-root>/administrator/components/com_civicrm/civicrm One thing that tricked me was that while I tried (and failed) to run the site with erroneous settings, CiviCRM created cached files in .../media/civicrm. But once I removed them the site worked fine without _updateConfigBackend_ and rebuild the menus as described below (but I suppose you can do them if needed).
1. One important change is the need to delete a more file called Config.IDS.ini. This WILL break CiviCRM if you don't do this. See below for details.


## Steps for non-Composer installations

See below if you have a Drupal 8 composer installation, or other composer installs.

1. Make a note of which CiviCRM modules are enabled on the original CMS.
1. Make a note of where your original sites' custom directories are and check if you have have any custom code you will need to copy later on in these steps. You can check this using the menu option "Administer >> System Settings >> Directories".
1. Disable but do not uninstall CiviCRM in your original CMS (unless you are moving the whole CMS).
1. Get a copy of the codebase in the new location.

    1. Copy the codebase files (use this option if any of the core files has been edited)

        1. If you decide not to do a fresh install, be sure to review the file paths in civicrm.settings.php, such as civicrm_root, civicrm_base, and CIVICRM_TEMPLATE_COMPILEDIR

            !!! danger
                If the new server or location is going to be a copy of a production system, to avoid duplicate emails going out, consider uncommenting and filling in an appropriate value in civicrm.settings.php to define 'CIVICRM_MAIL_LOG'.

        1. Execute a fresh install of your CMS to the new location.

            1. If you decide to do a fresh install, you will also need to recreate your (non-CiviCRM) installed module base.
            1. Do not copy your `civicrm.settings.php` from the old server, rather, install a clean copy of CiviCRM and merge any custom values needed (for example multi site variables or global settings overrides) into the new settings file
            1. If you are using a fresh install you need to make sure that the new version matches the version of your database.
                * Check in your live database "SELECT version FROM civicrm_domain" or check this value in the SQL export file you are using to move the database

1. Make sure any CiviCRM native modules used in the old install are installed in the new location.
1. Copy any custom code from the original site including the custom templates, custom reports, hooks etc. that you will find in your "Custom PHP Path Directory" and "Custom Templates" path–these paths you can find in CiviCRM via the "Administer – System Settings – Directories" page. Alternatively, if some of your custom code is in the form of CMS native modules then this custom code may be outside those CiviCRM folders. For example for Drupal this may often be found in <Drupal base>/sites/all/modules/custom/<yourcustomcmodule>.
1. Copy your databases.
    1. Drupal: Consider [Backup and Migrate Module](http://drupal.org/project/backup_migrate) to migrate the Drupal side. If your CiviCRM data is in a seperate database, Backup and Migrate can also backup your CiviCRM data but it cannot ignore tables. Ignored tables skirts alot of issues and this guide assumes that you are ignoring at least the civicrm_domain table.
    1. Command Line: _Substitute mysql_username, cms_db_name and dump_file_name_of_your_choice with appropriate values._**mySQL Dump for Drupal**

        ```
        On the old server:
        $ mysqldump -u mysql_username -p drupal_db_name > drupal_dump_file_name_of_your_choice.sql
        *** If using a fresh install you can skip the cache tables
        $ mysqldump -u mysql_username -p --ignore-table=civicrm_db_name.civicrm_domain \
        --ignore-table=civicrm_db_name.civicrm_acl_cache \
        --ignore-table=civicrm_db_name.civicrm_acl_contact_cache \
        --ignore-table=civicrm_db_name.civicrm_cache \
        --ignore-table=civicrm_db_name.civicrm_group_contact_cache \
        civicrm_db_name > civi_dump_file_name_of_your_choice.sql
        *** If copying an existing install don't skip the cache tables they will not be created automatically
        $ mysqldump -u mysql_username -p civicrm_db_name > civi_dump_file_name_of_your_choice.sql
        ```
        
        **mySQL Dump for Joomla**
        
        ```
        On the old server:
        $ mysqldump -u mysql_username -p --ignore-table=joomla_db_name.civicrm_domain \
        --ignore-table=joomla_db_name.civicrm_acl_cache \
        --ignore-table=joomla_db_name.civicrm_acl_contact_cache --ignore-table=joomla_db_name.civicrm_cache \
        --ignore-table=joomla_db_name.civicrm_group_contact_cache \
        joomla_db_name > dump_file_name_of_your_choice.sql
        ```

    !!! check "Multilingual Databases"
        For multilingual databases the owner of the multilingual views (the database username on the original server) will be included in the view definitions in the database export file. If the database username will be different on the new server you can find and replace all instances of the old username with the new one in the export file before importing it. |

1. Load your database(s) into to the new MySQL server

    1. Drupal: Again consider [Backup and Migrate Module](http://drupal.org/project/backup_migrate) to migrate the Drupal side.
    1. Command Line: _Substitute mysql_username, cms_db_name and dump_file_name_of_your_choice with appropriate values._**Load mySQL Dump for Drupal**
    
        ```
        On the new server:
        $ mysql -u mysql_username -p drupal_db_name < drupal_dump_file_name_of_your_choice.sql
        $ mysql -u mysql_username -p civicrm_db_name < civi_dump_file_name_of_your_choice.sql
        ```
        
        **Load mySQL Dump for Joomla**
        
        ```
        On the new server:
        $ mysql -u mysql_username -p joomla_db_name < dump_file_name_of_your_choice.sql
        ```
    
!!! note
    The above two steps, exporting the database from the source and importing to the target, 
    can also be performed in phpMyAdmin, although for exporting or importing a large database phpMyAdmin tends to time out
    and using command line is more reliable. The steps to export and import the database in phpMyAdmin are as follows:
    1. In the PHPMyAdmin interface, select your existing civiCRM database and then select the export feature.
    1. Under Export Method, select 'Quick' with format 'SQL.'
    1. Click the submit button to export your database which should then download somewhere onto your computer.
    1. Import the data by navigating to the new website's PHPMyAdmin system and doing a simple, 
    no frills import, using the data you just downloaded. The import should be into a new database created for CiviCRM 
    (or an existing database which has been emptied by deleting all tables), with suitable permissions for the database user 
    configured in the new sites civicrm.settings.php.

1. Delete files with cached settings

    * Drupal:

        * <drupal-root>/sites/default/files/civicrm/templates_c/* (cached versions of Smarty templates that will be rebuilt)
        * <drupal-root>/sites/default/files/civicrm/ConfigAndLog/Config.IDS.ini
        * <drupal-root>/sites/default/files/civicrm/ConfigAndLog/* (You can clear all the logs if you get an error about parsing XML)
        * <civicrm_custom_extension_folder>/cache/* (Only if you get errors after clearing the caches via the GUI) (See http://example.org/civicrm/admin/setting/path?reset=1 for location of custom extension folder)
        
    * Joomla:

        * <joomla-root>/media/civicrm/templates_c/*
        * <joomla-root>/media/civicrm//civicrm/ConfigAndLog/Config.IDS.ini

        !!! check "Be Careful"
            If you have edited the IDS ini file to add any custom exclusions then you need to edit the settings for the three paths in the file, filter_path, tmp_path, HTML_Purifier_Cache. If these paths are not accessible Civi pages will display blank.

    * WordPress:

        * <wordpress-root>/wp-content/plugins/files/civicrm/templates_c/*
        * <wordpress-root>/wp-content/plugins/files/civicrm/ConfigAndLog/Config.IDS.ini
        
1. Login to Drupal or to Joomla Administrator
1. Re-enable the CiviCRM module and any other CiviCRM sub-modules.
1. Enter the following URL in your browser to review and update directory paths and base URLs. See CiviCRM Menu: Administer >> System Settings >> Cleanup Caches and Update Paths

    * Drupal sites: http://example.org/index.php?q=civicrm/admin/setting/updateConfigBackend&reset=1
    * Joomla 1.5 sites: http://example.org/administrator/index2.php?option=com_civicrm&task=civicrm/admin/setting/updateConfigBackend&reset=1
    * Joomla 1.6 sites: http://example.org/administrator/index.php?option=com_civicrm&task=civicrm/admin/setting/updateConfigBackend&reset=1
    * WordPress sites: http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/updateConfigBackend&reset=1
        * Prior to 4.3.3 the WordPress implementation mistakenly drops everything after the domain in its suggestion for a new URL. The default location for a WordPress install relative to docroot would normally mean that the url should be http://example.org/wp-content/plugins/civicrm/civicrm/
        
1. Review the recommended modified paths in the form - they should reflect the new Base Directory, Base URL, and Site name for CiviCRM.
    1. If these values do NOT look correct, then recheck the changes you made to civicrm.settings.php. If everything looks right in your civicrm.settings.php file, you may want to follow the directions for setting `config_backend` to `null` in the Troubleshooting section (below).
        * **Base Directory** - For Drupal installs, this is the absolute path to the location of the 'files' directory. For Joomla installs this is the absolute path to the location of the 'media' directory.
        * **Base URL** - This is your Drupal or Joomla site URL (e.g. `http://example.org/drupal/`).
        * **Site Name** - The name of your Drupal sites subdirectory, starting from the docroot, and including an initial forward slash `/`, e.g. `/sites/www.mysite.com/`

    1. Otherwise click Save.
    1. You may also have to modify some of the other System Paths. If you had to copy custom hooks, reports etc. in a previous step look carefully at the "Custom PHP Path Directory" and "Custom Templates" path. See CiviCRM Menu: Administer >> System Settings >> Directories.
        * Drupal sites: http://example.org/index.php?q=civicrm/admin/setting/path?reset=1
        * Joomla 1.5 sites: http://example.org/administrator/index2.php?option=com_civicrm&task=civicrm/admin/setting/path?reset=1
        * Joomla 1.6 sites: http://example.org/administrator/index.php?option=com_civicrm&task=civicrm/admin/setting/path?reset=1
        * WordPress sites: http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/path?reset=1
    1. Rebuild the menus. The menu links in the civicrm_menu table contain full URL links and need to be updated with the new domain.
        * Drupal sites: _http://example.org/index.php?q=civicrm/menu/rebuild&reset=1_
        * Joomla 1.5 sites: _http://example.org/administrator/index2.php?option=com_civicrm&task=civicrm/menu/rebuild&reset=1_
        * Joomla 1.6 sites: _http://example.org/administrator/index.php?option=com_civicrm&task=civicrm/menu/rebuild&reset=1_
        * _WordPress sites: http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/menu/rebuild&reset=1_
    1. Rebuild database triggers. The trigger definitions in your database refer to the original database user.
        * Drupal sites: _http://example.org/civicrm/menu/rebuild?reset=1&triggerRebuild=1_
        * Joomla 1.5 sites: _http://example.org/administrator/index2.php?option=com_civicrm&task=civicrm/menu/rebuild&reset=1&triggerRebuild=1_
        * Joomla 1.6 sites:_http://example.org/administrator/index.php?option=com_civicrm&task=civicrm/menu/rebuild&reset=1&triggerRebuild=1_
        * WordPress sites: _http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/menu/rebuild&reset=1&triggerRebuild=1_
    1. Joomla 1.6/1.7 - Edit the path in /administrator/components/com_civicrm/civicrm/civicrm.config.php which may still use your old location.
    1. If you made any non-CiviCRM changes to your site (e.g. exposed profiles on the Drupal nav bar) don't forget to fix (e.g. for Drupal: Administer >> Site building >> Menus >> Navigation).

        !!! forbidden
            Your 'files' directory is typically outside of your CiviCRM install directory. The below step is important for proper operation on a new site.

    1. Go to **Administer > System Settings**. If you are using custom templates or PHP then you'll need to click on **Directories** to update the directory locations for these (/civicrm/admin/setting/path?reset=1). You should click on **Resource URLs** to ensure these are correct.
    1. Ensure that your cron is working on your new server. Depending on how it was installed and whether you are decommissioning the previous server, you may find that decommissioning the old server is when it stops working.
    1. If you previously set up a log rotation for your CiviCRM error log file, eg sites/default/files/civicrm/ConfigAndLog/CiviCRM.*.log, you should do this again.

### Additional Steps if your URL has changed

When moving your server, it's quite likely that you've changed your URL. If so, please make sure to update the URL in the following locations:

1. If you use a payment processor with ARB (recurring payments), and that processor notifies your server via IPN (e.g. PayPal, Authorize.Net), you must update the IPN in your payment processor's settings page. Please note that for PayPal, you can only change IPNs for new recurring payment profiles. To handle your old IPNs, you willneed to set up a [307 redirect](http://civicrm.stackexchange.com/questions/473/how-to-change-paypal-ipn-url-when-migrating-civicrm-onto-a-new-domain) from the old URL.
1. URLs linking to contribution pages etc. in mailing templates, scheduled reminders etc. need to be changed.
1. Images in mailing templates and scheduled reminders must get a new absolute URL (or a redirect).

### Other notes

As a result of your migration, many of your site's other configuration settings have been reset, for instance Available Countries in "Localization" and reCaptcha keys. Page through the _Configuration Checklist_ at **Administer > Administration Console > Configuration Checklist** to be sure that all your settings are once again set to their correct values.

Your site should now be properly configured for its new location.

## Steps for Composer installations

This documentation covers the general steps to move a CiviCRM instance in a Drupal 8 website to a new server or location. Refer to Drupal documentation for instructions on how to move the rest of the Drupal 8 site.

Some settings in the CiviCRM database relate to a physical location, such as a server file path or a public URL. These settings are usually stored as relative paths (with respect to the default file container, eg sites/default/files/civicrm or media/civicrm), and this is the recommended best practice. But sometimes an administrator user can customise them to use an absolute path. If you have any absolute paths, then you must change these when moving an existing CiviCRM installation to a new server as described below.

Following are the instructions to migrate an existing CiviCRM instance to a New Server/Location:

Make a note of where your original sites' custom directories are and check if you have have any custom code you will need to copy later on in these steps. You can check this using the CiviCRM menu option "Administer >> System Settings >> Directories".

Make a note of which CiviCRM dependent modules are enabled on the original CMS.

Unlike in Drupal 7, Drupal 8 does not allow a module to be disabled without uninstalling it and removing all of its database entries (for more details see here). We have not tested the best approach for migrating sites that have Drupal modules dependent on CIviCRM enabled, like CiviCRM Group Roles, CiviCRM Member Roles, CiviCRM Views, CiviCRM Entity etc. For now you might want to try copying both the Drupal and CiviCRM systems at the same time so they stay in sync, and at worst assume you’ll need to uninstall the modules and later rebuild their configurations on the new instance.

Copy the entire codebase from the old to the new location. (We hope at some point to rebuild it from composer.json and composer.lock using composer, but at present the install uses composer, bower and node_dependencies and more work is needed to deal with bower and node_dependencies.) Make sure to review the file paths in sites/default/files/civicrm/civicrm.settings.php, such as civicrm_root, CIVICRM_UF_BASEURL and CIVICRM_TEMPLATE_COMPILEDIR etc. This can be done by tar’ing or gzip'ing up the code, moving it to the new server/location, and untar’ing or gunzip’ing. The default locations to copy are:

docroot/vendor/civicrm

docroot/default/files/civicrm

Make sure any CiviCRM dependent modules used in the old install are installed in the new location.

Make sure CiviCRM package is present in vendor directory.

Copy your database via command line (there are some cache tables you could skip the contents of, but it’s usually more trouble than it’s worth to do that):

On the old server:
$ mysqldump -u mysql_username -p drupal_db_name > drupal_dump_file_name_of_your_choice.sql
$ mysqldump -u mysql_username -p civicrm_db_name > civi_dump_file_name_of_your_choice.sql
8. Load your database(s) into to the new MySQL server

On the new server:
$ mysql -u mysql_username -p drupal_db_name < drupal_dump_file_name_of_your_choice.sql
$ mysql -u mysql_username -p civicrm_db_name < civi_dump_file_name_of_your_choice.sql
NOTE: You can find the DB credentials of Drupal 8 and CiviCRM in civicrm.settings.php, check CIVICRM_UF_DSN for D8 and CIVICRM_DSN for Civi.

9. Delete files with cached settings:
a. /sites/default/files/civicrm/templates_c/* (cached versions of Smarty templates that will be rebuilt)

b. /sites/default/files/civicrm/ConfigAndLog/Config.IDS.ini

c. /sites/default/files/civicrm/ConfigAndLog/* (You can clear all the logs if you get an error about parsing XML)
d. /cache/* (Only if you get errors after clearing the caches via the GUI) (See http://example.org/civicrm/admin/setting/path?reset=1 for location of custom extension folder)

10. Login to new Drupal8 site.
11. Clear caches (and rebuild the menus) and Reset Paths by navigating to Administer > Cleanup Caches and Update Paths (https://www.example.com/civicrm/admin/setting/updateConfigBackend?reset=1). Click the Cleanup Caches and Reset Paths buttons in turn.

12. You may also have to modify some of the other System Paths to non-default values. Click on the Directories button or navigate to Administer > System Settings > Directories Navigate to Administer > System Settings > Directories (https://www.example.com/civicrm/admin/setting/path?reset=1). If you had to copy custom hooks, reports etc. in a previous step look carefully at the "Custom PHP Path Directory" and "Custom Templates" path.

13. You may need to adjust URLs if the directories above changed. Navigate to Administer > System Settings > Resource URLs (https://www.example.com/civicrm/admin/setting/url?reset=1). The help icon at the end of the introductory paragraph provides a useful explanation of how to configure relative URLs. Review the recommended modified URL and paths in the form - they should reflect the new Base Directory, Base URL. If these values do NOT look correct, then recheck the changes you made to civicrm.settings.php.

14. If you made any non-CiviCRM changes to your site (e.g. exposed profiles on the Drupal nav bar) don't forget to fix them (e.g. at Drupal: Administer >> Site building >> Menus >> Navigation).

15. On an operating command line or in a Control Panel, etc., ensure that your cron is working on your new server. Depending on how it was installed and whether you are decommissioning the previous server, you may find that decommissioning the old server is appropriate when it stops working. Note that it is often a best practice to NOT run cron jobs on staging and dev sites when production databases are copied down to them. This can result in non-production sites sending additional copies of CiviMails, and submitting additional payment requests (eg for certain payment processors with integration software not intended for easy and safe use in dev/staging/prod setups, eg iATS). Also, some sites that email reports or execute other similar jobs need to have multiple crons setup.

16. After initial setup there should not be a need to make changes at Administer > System Settings > CMS Database Integration: https://www.example.com/civicrm/admin/setting/uf?reset=1

## Trouble-shooting

### Only the front page of your site can be displayed - Drupal

If you are using drupal on Ubuntu, you may find that your site looks to be working until you click on a link or go to any other url than the front page. This can be caused by the default setting for the Apache mod-rewrite module. If you can load url's like, "http://example.org/index.php?q=/civicrm/admin/setting", but not "http://example.org/civicrm/admin/setting, then this is likely the cause.

Use the following article, or many others on the web to enable mod_rewrite. -- [http://drupal.org/node/134439](http://drupal.org/node/134439)

### Only the front page of your site can be displayed - Joomla

I just had an issue getting to the page where the directories can be updated. There was an error with Joomla 1.7 / CiviCRM 4.0.5 on a site that had been upgraded a few times already: Failed opening required 'CRM/Core/Config.php. I solved this by manually editting [DOCROOT]/administrator/components/com_civicrm/civicrm.settings.php and [DOCROOT]/components/com_civicrm/civicrm.settings.php to replace incorrect values for $civicrm_root, CIVICRM_TEMPLATE_COMPILEDIR, and CIVICRM_UF_BASEURL (the latter since I was using a different URL on my local dev machine).

### Unable to update Config Backend

This process should avoid this error, however if after entering the "updateConfigBackend" URL you may get an error suggesting you need to set the Config_Backend column to NULL. Please execute the following sql in your database (using mysql shell or PHPMyAdmin)

!!! forbidden
    This field contains many other useful CiviCRM configuration settings, such as what components are enabled, your mailserver setting, what countries are enabled, etc. You should only NULL this as a last resort.


```
USE <yourdatabasename>;
UPDATE civicrm_domain SET config_backend = null;
```

1. this was not enough for me. I entered a path that was incorrect in the php and extension options. I had to go into the database and find the path the error message noted it was trying to make and replace it in civicrm_option_value. To find it I ran

    ```sql
    SELECT *
    FROM `civicrm_option_value`
    WHERE `value` LIKE '%PATH_HAVING_TROUBLE_WITH%'
    LIMIT 0 , 30
    ```

Then I did an update to the two rows it found.
 In this case it was the row labeled Custom PHP and Custom Extensions.

### Table 'db_name.civicrm_option_value_en_us' doesn't exist

The error occurs on multi-lingual migrations when the source server is not case sensitive but the destination server is. To correct this error, edit the database dump in a text editor and globally find and replace your language codes to the proper case. The second pair of letters should be upper case, as in en_US.

### Database Error Code: FUNCTION ... does not exist

This seems to be caused by the database backup and restore not copying the functions and triggers properly.

The solution is to run `http://example.org/civicrm/menu/rebuild?reset=1&triggerRebuild=1` which fixes the trigger and function definitions in the database.

On Joomla sites run `http://example.org/administrator/?option=com_civicrm&task=civicrm/menu/rebuild&reset=1&triggerRebuild=1`
