# Moving an Existing Installation to a New Server or Location

Some settings in the CiviCRM database relate to a physical location, such as a server file path or a public URL. These settings are usually stored as relative paths (with respect to the default file container, eg `sites/default/files/civicrm` or `media/civicrm`), but sometimes an admin customizes them to use an absolute path. If you have any absolute paths, then you must usually change these when moving an existing CiviCRM installation to a new server. This documentation covers the general steps.

**If you regularly copy a production database to a test server, you can [use a settings override](https://wiki.civicrm.org/confluence/display/CRMDOC/Override+CiviCRM+Settings) to avoid having to "fix" the directory and URL-based settings each time you copy the database over.**

_Older versions of CiviCRM included a script which performed a heuristic search-replace against file paths and URLs. The script had various interfaces (e.g. `doSiteMove()`, `civicrm/admin/setting/updateConfigBackend`, `bin/migrate/move.php`, `drush civicrm-update-cfg`, `wp civicrm update-cfg`.) But it was not maintained well and will be removed in v4.7. Best advice: stick to relative paths under the default file container, or use a settings-override for absolute paths. (See also: [forum discussion](https://forum.civicrm.org/index.php?topic=36692.msg155904#msg155904))_

These instructions have been rewritten based on my recent migration experience and are also informed by this blog post: [http://civicrm.org/blogs/eileen/drush-bam-civi-wham](http://civicrm.org/blogs/eileen/drush-bam-civi-wham). The overall process I am suggesting is to install a fresh working copy of CiviCRM on your new server. Next copy any custom code then move the majority of your database from the old server, ignoring domain and cache tables. This leaves the good working domain data in place on your new server.

Some sites may have been advised to create a file `/path/to/civicrm/settings_location.php`, which hardcodes the path to the site configuration directory. This is used to work around issues with deducing the site configuration path. If your site has such a file, it may require updating along with the hardcoded paths in `civicrm.settings.php`

If you are using Joomla, Akeeba backup is a handy plugin to create complete backups of your site, including CiviCRM. It does not only copy your database but all your files too. Configure Akeeba to ignore tables, table content and files as described below (Note, sometimes it is best to NOT ignore the civicrm_domain table. If you get an error on your copy when go to access the Civi pages, try NOT ignoring that table in your Akeeba backup). Such a backup can be restored easily and completely using its complementary Kickstart script on any site, not just your original site, because it contains all restoration scripts necessary to get you up and running.
 If you are transferring to a new host some paths and URLs probably have changed so you must update:

1. all URLs that points to your old site in both the civicrm.settings.php (in .../administrator/components/com_civicrm and .../components/com_civicrm) as described below
1. all database connection strings in both the civicrm.settings.php (in .../administrator/components/com_civicrm and .../components/com_civicrm) as described below
1. all file paths to the installation in both the civicrm.settings.php files *as well as* the civicrm.config.php file which resides in <joomla-root>/administrator/components/com_civicrm/civicrm One thing that tricked me was that while I tried (and failed) to run the site with erroneous settings, CiviCRM created cached files in .../media/civicrm. But once I removed them the site worked fine without _updateConfigBackend_ and rebuild the menus as described below (but I suppose you can do them if needed).
1. One important change is the need to delete a more file called Config.IDS.ini. This WILL break CiviCRM if you don't do this. See below for details.


## Steps

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
            1. Do not copy your civicrm.settings.php from the old server, rather, install a clean copy of CiviCRM: [Installation and Upgrades](https://wiki.civicrm.org/confluence/display/CRMDOC/Installation+and+Upgrades) and merge any custom values needed (for example multi site variables or global settings overrides) into the new settings file
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
    
1. Delete files with cached settings

    * Drupal:

        * <drupal-root>/sites/default/files/civicrm/templates_c/* (cached versions of Smarty templates that will be rebuilt)
        * <drupal-root>/sites/default/files/civicrm/ConfigAndLog/Config.IDS.ini
        * <drupal-root>/sites/default/files/civicrm/ConfigAndLog/* (You can clear all the logs if you get an error about parsing XML)
        * <civicrm_custom_extension_folder>/cache/* (Only if you get errors after clearing the caches via the GUI) (See http://<drupal-site>/civicrm/admin/setting/path?reset=1 for location of custom extension folder)
        
    * Joomla:

        * <joomla-root>/media/civicrm/templates_c/*
        * <joomla-root>/media/civicrm//civicrm/ConfigAndLog/Config.IDS.ini

        !!! check "Be Careful"
            If you have edited the IDS ini file to add any custom exclusions then you need to edit the settings for the three paths in the file, filter_path, tmp_path, HTML_Purifier_Cache. If these paths are not accessible Civi pages will display blank.

    * Wordpress:

        * <wordpress-root>/wp-content/plugins/files/civicrm/templates_c/*
        * <wordpress-root>/wp-content/plugins/files/civicrm/ConfigAndLog/Config.IDS.ini
        
1. Login to Drupal or to Joomla Administrator
1. Re-enable the CiviCRM module and any other CiviCRM sub-modules.

    !!! check
        You can also override the settings in your local civicrm.settings.php and avoid going to some of the below urls. Check: [Override Server Directory and URL Settings](https://wiki.civicrm.org/confluence/display/CRMDOC42/Override+Server+Directory+and+URL+Settings) |

1. Enter the following URL in your browser to review and update directory paths and base URLs. See CiviCRM Menu: Administer >> System Settings >> Cleanup Caches and Update Paths

    * Drupal sites: http://<drupal_site>/index.php?q=civicrm/admin/setting/updateConfigBackend&reset=1
    * Joomla 1.5 sites: http://<joomla_site>/administrator/index2.php?option=com_civicrm&task=civicrm/admin/setting/updateConfigBackend&reset=1
    * Joomla 1.6 sites: http://<joomla_site>/administrator/index.php?option=com_civicrm&task=civicrm/admin/setting/updateConfigBackend&reset=1
    * Wordpress sites: http://<wordpress_site>/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/updateConfigBackend&reset=1
        * Prior to 4.3.3 the WordPress implementation mistakenly drops everything after the domain in its suggestion for a new URL. The default location for a WordPress install relative to docroot would normally mean that the url should be http://<wordpress_site>/wp-content/plugins/civicrm/civicrm/
        
1. Review the recommended modified paths in the form - they should reflect the new Base Directory, Base URL, and Site name for CiviCRM.
    1. If these values do NOT look correct, then recheck the changes you made to civicrm.settings.php. If everything looks right in your civicrm.settings.php file, you may want to follow the directions for setting config_backend to null in the Troubleshooting section (below).
        * **Base Directory** - For Drupal installs, this is the absolute path to the location of the 'files' directory. For Joomla installs this is the absolute path to the location of the 'media' directory.
        * **Base URL** - This is your Drupal or Joomla site URL (e.g. [http://www.mysite.com/drupal/](http://www.mysite.com/drupal/)).
        * **Site Name** - The name of your Drupal sites subdirectory, starting from the docroot, and including an initial /, e.g. /sites/www.mysite.com/ |

    1. Otherwise click Save.
    1. You may also have to modify some of the other System Paths. If you had to copy custom hooks, reports etc. in a previous step look carefully at the "Custom PHP Path Directory" and "Custom Templates" path. See CiviCRM Menu: Administer >> System Settings >> Directories.
        * Drupal sites: http://<drupal_site>/index.php?q=civicrm/admin/setting/path?reset=1
        * Joomla 1.5 sites: http://<joomla_site>/administrator/index2.php?option=com_civicrm&task=civicrm/admin/setting/path?reset=1
        * Joomla 1.6 sites: http://<joomla_site>/administrator/index.php?option=com_civicrm&task=civicrm/admin/setting/path?reset=1
        * WordPress sites: http://<wordpress_site>/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/path?reset=1
    1. Rebuild the menus. The menu links in the civicrm_menu table contain full URL links and need to be updated with the new domain.
        * Drupal sites: _http://<drupal_site>/index.php?q=civicrm/menu/rebuild&reset=1_
        * Joomla 1.5 sites: _http://<joomla_site>/administrator/index2.php?option=com_civicrm&task=civicrm/menu/rebuild&reset=1_
        * Joomla 1.6 sites: _http://<joomla_site>/administrator/index.php?option=com_civicrm&task=civicrm/menu/rebuild&reset=1_
        * _WordPress sites: http://<wordpress_site>/wp-admin/admin.php?page=CiviCRM&q=civicrm/menu/rebuild&reset=1_
    1. Rebuild database triggers. The trigger definitions in your database refer to the original database user.
        * Drupal sites: _http://<drupal_site>/civicrm/menu/rebuild?reset=1&triggerRebuild=1_
        * Joomla 1.5 sites: _http://<joomla_site>/administrator/index2.php?option=com_civicrm&task=civicrm/menu/rebuild&reset=1&triggerRebuild=1_
        * Joomla 1.6 sites:_http://<joomla_site>/administrator/index.php?option=com_civicrm&task=civicrm/menu/rebuild&reset=1&triggerRebuild=1_
        * WordPress sites: _http://<wordpress_site>/wp-admin/admin.php?page=CiviCRM&q=civicrm/menu/rebuild&reset=1&triggerRebuild=1_
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

## Trouble-shooting

### Only the front page of your site can be displayed - Drupal

If you are using drupal on Ubuntu, you may find that your site looks to be working until you click on a link or go to any other url than the front page. This can be caused by the default setting for the Apache mod-rewrite module. If you can load url's like, "http://<yoursite>/index.php?q=/civicrm/admin/setting", but not "http://<yoursite>/civicrm/admin/setting, then this is likely the cause.

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

The solution is to run `http://<yourdomain>/civicrm/menu/rebuild?reset=1&triggerRebuild=1` which fixes the trigger and function definitions in the database.

On Joomla sites run `http://<yourdomain>/administrator/?option=com_civicrm&task=civicrm/menu/rebuild&reset=1&triggerRebuild=1`



## Moving the database using phpMyAdmin

This is an alternate method for moving a civiCRM installation from one server to another which is similar to the instructions: [Moving the database using phpMyAdmin](https://wiki.civicrm.org/confluence/display/CRMDOC/Moving+the+database+using+phpMyAdmin)

This method has worked well for moving Drupal/CiviCRM based websites from development servers to production servers and uses the Backup and Migrate module for transferring the Drupal data and PHPMyAdmin for the civiCRM data.

### Preliminary Setup

These are the steps I take to prepare a new installation of Drupal and civiCRM for taking on the content and CRM data of an existing production site or moving a site from a development environment to a live production server:

1. Before moving the site make sure that the Drupal/CiviCRM development site is up to date with core and contributed modules.

1. On the new server install a fresh installation of Drupal using the same version as that of the site you are transferring. I start fresh with files from Drupal.org rather than transferring my files from the development server to ensure that they have not been compromised somewhere during the development process.

1. Once an empty Drupal site is installed and working correctly, I move my `/sites/all` directories into the Drupal installation. This gives me my themes, modules and libraries.
**Note:** I _don't move_ `/sites/all/modules/civicrm because` I am more comfortable using a new copy from the civiCRM website, just in case something has been compromised during development or transferring.

1. I also move select directories from my `/sites/default/files` directory.
**Note:** I _don't move_ any of the cached directories under `/sites/default/files` and I don't move `/sites/default/files/civicrm`

1. Next, I install a brand new copy of civiCRM into the website by running the standard installation script from within `/sites/all/modules/civicrm/install/`
**Note:** Go thorough the configuration checklist just to make sure everything appears to be correct but don't spend a lot of time on it because it will be overwritten when you transfer the data.

1. At this point, the new site should be an empty canvas with all of the components of the site that you are moving but without any of the data, design or structure yet. Make sure that you have the site theme on hand and that all of the modules that are present in the site that you are moving. Also, have a look at the site and server reports to ensure that everything is functioning as it should without any errors or resource problems.


### Export/Transfer Drupal CMS Data

For transferring Drupal site data I like to use the excellent Backup and Migrate module ([http://drupal.org/project/backup_migrate](http://drupal.org/project/backup_migrate)) but you can use PHPMyAdmin if that makes you more comfortable. Transferring the Drupal data for a Drupal/civiCRM website is no different than any other Drupal site but do not use the Backup and Migrate module for moving the civiCRM data. In my experience it will not work.

At this point I transfer my Drupal data and the site should have all of the CMS data, navigation and theming intact. Only CRM data is missing now.

### Export/Transfer civiCRM Data

Exporting the civiCRM data can be a bit tricky because civiCRM seems intimately aware of its server/database environment. There are a number of areas established during installation where civiCRM keeps track of its surroundings and if we aren't careful we will overwrite them and then we'll have to start from scratch. It's not that bad but it gets a little tedious once you've gotten to the 80% done part and have to start over.

The following points cover how I export data using PHPMyAdmin and avoid tripping myself up:

1. In the PHPMyAdmin interface, select your existing civiCRM database and then select the export feature.

1. Under Export Method, select: _**"Custom - display all possible options"**_

1. In the tables area deselect (hold down control or option depending on OS and deselect the following 5 tables):

    1. civicrm_acl_cache
    1. civicrm_acl_contact_cache
    1. civicrm_cache
    1. civicrm_domain
    1. civicrm_group_contact_cache

1. Make sure that you check the "Disable foreign key checks" option.
1. Under Object Creation Options check the "Add DROP TABLE / VIEW / PROCEDURE / FUNCTION / EVENT statement" option
1. Click the submit button to export your table which should then download somewhere onto your computer.
1. Import the data by navigating to the new website's PHPMyAdmin system and doing a simple, no frills import using the data you just downloaded. It should just import the data and overwrite the existing tables with your civiCRM data.

### Post Import Activities

After you have imported the civiCRM data, there are some tasks that need to be done in order to flush out caches and provide a clean slate for civiCRM and Drupal to work together.

1. I always run the following SQL queries that I gleaned from the civiCRM support Forums
    * On your civiCRM Database Run
        1. truncate civicrm_uf_match;
        1. update civicrm_domain set config_backend = null;

    * On your Drupal Database run:
        1. truncate sessions;

1. In /sites/default/files/civicrm, delete or rename the folder `templates_c`. A new one will be created when civiCRM is run.
1. Navigate to the civiCRM Configuration Checklist ([www.example.com/civicrm/admin/configtask?reset=1)](http://www.example.com/civicrm/admin/configtask?reset=1%29) and review the setting to make sure that they are correct. More than likely the settings for language, default country and other regional settings will be incorrect. Take extra care to ensure that your directories are set up correctly, especially if you use custom templates for one reason or another.
1. **Very Important:** If you have custom data profiles set up in your civiCRM, they may be present but for some unknown reason not be active. Go to your Administer >> Customize >> Custom Data and select _**"View and Edit Custom Fields"**_. Select **_"Edit Field"_** for any one of the custom fields in the group and save it without making any changes. This will make civiCRM aware of the custom data fields and everything should be back on track.
1. If you have Drupal user Profiles that relate to the civiCRM contacts, you will want to run **_"Synchronize Users to Contacts"_**. It can be found at Administer >> Manage >> Synchronize Users to Contacts

I think that covers my process. If I come up with anything else I will edit the Wiki to reflect the changes. If anyone else can add to this, please feel free to do so.
