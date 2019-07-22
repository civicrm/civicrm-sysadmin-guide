# Switching CMS platforms

This page provides instructions for moving your CiviCRM installation from being installed within one CMS to being installed within another.  If you are changing server at the same time, please also refer to the instructions on [Moving an Existing Installation to a New Server or Location](/misc/switch-servers.md).

## Joomla to Drupal

### Preparing for the migration

* Backup your system
    * Backup the CiviCRM database that was set up for Joomla.
    * Backup the Drupal site before making any changes to it.

        !!! tip
            If you are going to be importing and overwriting your older CiviCRM database over the newly installed, blank CiviCRM database, adding IF NOT EXISTS clauses to the CREATE TABLE statements will help the import. DROP TABLE statements will fail because of foreign key restraints.

    * Set up CiviCRM in your Drupal site

        !!! check
            When downloading the CiviCRM package get the same version of the CiviCRM package for Drupal as the version you used in Joomla. If you want to upgrade to a newer version of CiviCRM, upgrade it before or after – not during – the migration.

        Refer to the [Drupal Installation Guide](/install/drupal7.md) for details on obtaining and setting up CiviCRM for Drupal.

### Restore the CiviCRM database backup to the desired location

* The installation process would have set up new database tables for CiviCRM. If this is where you want your CiviCRM database to be, then you may overwrite these tables by restoring your old database over it.
* Alternatively you can restore your CiviCRM database somewhere else and modify **CIVICRM_DSN** in _<drupal_root>/sites/default/civicrm.settings.php_ to point to this new database.

### Empty the `civicrm_uf_match` table

This table is used for mapping your CMS users with CiviCRM contacts. At this stage those mappings will be according to users from your Joomla site. You will need to delete these mappings so that Drupal can then automatically rebuild them from the new users table.

```
TRUNCATE TABLE `<civicrm_database>`.`civicrm_uf_match`;
```

### Update the Directory Path and URL

* Log in to Drupal with a username that has administrator access to CiviCRM
* Go to **http://example.org/civicrm/admin/setting/updateConfigBackend?reset=1**
    * Set your base directory to _<drupal_root>/sites/default/files/_
    * Ensure your base URL is correct
    * Click **Save**

### Update the Resource URLs

Up to this stage, CiviCRM screens are missing all of its CSS formatting and images because the paths to these resources are incorrect. This step is to set the correct paths to these resources.

* Go to **http://example.org/civicrm/admin/setting/url?reset=1**
    * Set CiviCRM Resource URL to _http://example.org/sites/all/modules/civicrm/_
    * Set Image Upload URL to _http://example.org/sites/default/files/civicrm/persist/contribute/_
    * Click **Save**

### Rebuild the menus and triggers

The menu links in the civicrm_menu table contains URLs for the menu links. These links need to be updated since CiviCRM uses a different URLs in Joomla.

* Go to **http://example.org/civicrm/menu/rebuild?reset=1**. Alternatively, delete all the records in civicrm_menu:
    ```
    TRUNCATE TABLE `<civicrm_database>`.`civicrm_menu`;
    ```
    
* In the CiviCRM database, set the `civicrm_domain.config_backend` field to `NULL`
    ```
    UPDATE `<civicrm_database>`.`civicrm_domain` SET `config_backend` = NULL
    WHERE `civicrm_domain`.`id` = 1 LIMIT 1;
    ```
    
* Go to **http://example.org/civicrm/menu/rebuild?reset=1&triggerRebuild=1.** This will rebuild your triggers

### Drupal table prefixes

If you have table prefixes on your Drupal database you'll also need to do the following to allow CiviMail cronjobs to run, etc.

* Go to **http://example.org/civicrm/admin/setting/uf?reset=1**
    * Update Drupal Users Table Name with your_table_prefix_users (eg. drupal_users)
    * Click **Save**

### Update screen editor

If you're having trouble editing and/or adding users, check your WYSIWYG setting under Display Preferences

* Go to **http://example.org/civicrm/admin/setting/preferences/display&reset=1**
* Check the WYSIWYG editor setting and choose one of the selections (this resets to a Drupal choice vs. Joomla)
* Click **Save**

!!! check
    The CiviCRM sidebar may disappear at this point. This is because in Joomla the sidebar is fixed. In Drupal you are able to set the position of these components as **blocks** on the page.


!!! tip
    You may find the CiviCRM screens do not fit comfortably on the page if you are using a fixed width Drupal theme. A solution is to use the [civicrm_theme](http://drupal.org/project/civicrm_theme) module that enables you to choose a different theme for the CiviCRM screens.
    


## Drupal to WordPress

### Preparing for the migration

* Backup your system
    * Backup the CiviCRM database that was set up for Drupal.
    * Backup the WordPress site before making any changes to it.

        !!! tip
            If you are going to be importing and overwriting your older CiviCRM database over the newly installed, blank CiviCRM database, adding IF NOT EXISTS clauses to the CREATE TABLE statements will help the import. DROP TABLE statements will fail because of foreign key restraints.

* Set up CiviCRM in your WordPress site

    !!! tip
        When downloading the CiviCRM package get the same version of the CiviCRM package for WordPress as the version you used in Drupal. If you want to upgrade to a newer version of CiviCRM, upgrade it before or after – not during – the migration.
    
     Refer to the [WordPress Installation Guide](/install/wordpress.md) for details on obtaining and setting up CiviCRM for WordPress.


!!! tip "WordPress Admin Account"
    When you set up WordPress, make sure you use the same Administrator account name as the Drupal Administrator account. Otherwise, you might encounter DB errors and/or Foreign Constraint issues due to the WordPress account not being associated with the CRM Accounts. If you have no choice, make sure you synchronize your accounts prior to trying to edit anything else.

### Restore the CiviCRM database backup to the desired location

* The installation process would have set up new database tables for CiviCRM. If this is where you want your CiviCRM database to be, then you may overwrite these tables by restoring your old database over it.
* Alternatively you can restore your CiviCRM database somewhere else and modify **CIVICRM_DSN** in _<drupal_root>/sites/default/civicrm.settings.php_ to point to this new database.

### Empty the `civicrm_uf_match` table

This table is used for mapping your CMS users with CiviCRM contacts. At this stage those mappings will be according to users from your Drupal site. You will need to delete these mappings so that WordPress can then automatically rebuild them from the new users table.

```sql
TRUNCATE TABLE `<civicrm_database>`.`civicrm_uf_match`;
```

### Relocate Custom Files

* On your old site, visit Administer > System Settings > Directories and Administer > System Settings > Resource URL's.
* Copy all of your custom files and uploads to the appropriate directories on your new site.
 In WordPress, these will likely be under wp-content/plugins/files/civicrm/civicrm
 In drupal, these will likeley be under sites/default/files/civicrm.


### Update the Base Directory Path and URL

* Log in to WordPress with a username that has administrator access to CiviCRM
* Go to _**http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/updateConfigBackend&reset=1**_
    * Set your base directory to _<wordpress_root>/wp-content/plugins/files/_
 Ensure your base URL is correct - it should be _http://example.org/_ (the main URL of your site)
    * Click **Save**

### Update the Resource URLs

Up to this stage, CiviCRM screens are missing all of its CSS formatting and images because the paths to these resources are incorrect. This step is to set the correct paths to these resources.

* Go to _**http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/url&reset=1**_
    * Set CiviCRM Resource URL to _http://example.org/wp-content/plugins/civicrm/civicrm/_
    * Set Image Upload URL to _http://example.org/wp-content/plugins/files/civicrm/persist/contribute/_ (may need to be created)
    * Click **Save**

### Rebuild the menus

The menu links in the civicrm_menu table contains URLs for the menu links. These links need to be updated since CiviCRM uses a different URLs in Drupal.

* Go to _**http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/menu/rebuild&reset=1**_. Alternatively, delete all the records in civicrm_menu:
    ```sql
    TRUNCATE TABLE `<civicrm_database>`.`civicrm_menu`;
    ```
    
* In the CiviCRM database, set the `civicrm_domain.config_backend` field to `NULL`
    ```sql
    UPDATE `<civicrm_database>`.`civicrm_domain`
    SET `config_backend` = NULL
    WHERE `civicrm_domain`.`id` = 1 LIMIT 1;
    ```

### WordPress table prefixes

If you have table prefixes on your Drupal database you'll also need to do the following to allow CiviMail cronjobs to run, etc.

* Go to **http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/uf&reset=1**
    * Update WordPress Users Table Name with your_table_prefix_users (eg. wp_users) You may have changed this in accordance with WordPress best practices to have a different prefix other than wp_
    * Click **Save**

### Update the changed URL in your user data

When switching CMSes, your URLs will ALWAYS be changed. Please review the documentation on [this page](/misc/switch-servers.md) under the heading "Additional Steps if your URL has changed".

### Problems

If you have a DBA with a solid understanding of CiviCRM you can manually migrate tables leaving out ACL tables, cache tables, and any tables which don't have data, but audit your migration thoroughly before opening for business. Do not add any data until it has been satisfactorily audited to confirm data is complete.




## Drupal to Backdrop

Since an upgrade path from Drupal 7 to Drupal 8 is too complex for some users, migrating to Backdrop is a great alternative.

!!! Migrating Drupal 7 CMS to Backdrop CMS
        For information about migrating to Backdrop from Drupal, see [Upgrading from Drupal 7](https://backdropcms.org/upgrade-from-drupal) on the Backdrop website.

### Preparing for the Migration

* Backup the CiviCRM database that was set up for Drupal.
* Backup the Backdrop site before making any changes to it.
* Set up CiviCRM in your Backdrop site

    !!! check
            When downloading the CiviCRM package get the same version of the CiviCRM package for Backdrop as the version you used in     Drupal. If you want to upgrade to a newer version of CiviCRM, upgrade it before or after – not during – the migration.

    Refer to the [Backdrop Installation Guide](install/backdrop.md) for details on obtaining and setting up CiviCRM for Backdrop.

### Relocate Custom Files

* Copy any extension directories from the previous Drupal directory to the new Backdrop directory at _<backdrop_root>/files/civicrm/ext_
* Copy any sensitive uploads/attachments from the previous Drupal directory to the new Backdrop directory at _<backdrop_root>/files/civicrm/custom_
* Copy any template overrides from the previous Drupal directory to the new Backdrop directory at _<backdrop_root>/files/civicrm/templates_
* Copy any uploaded media from the previous Drupal directory to the new Backdrop directory at _<backdrop_root>/files/civicrm/persist_

### Restore the CiviCRM database backup to the desired location

* The installation process would have set up new database tables for CiviCRM. If this is where you want your CiviCRM database to be, then you may overwrite these tables by restoring your old database over it.
* Alternatively you can restore your CiviCRM database somewhere else and modify **CIVICRM_DSN** in _<backdrop_root>/civicrm.settings.php_ to point to this new database.

### Empty the `civicrm_uf_match` table

This table is used for mapping your CMS users with CiviCRM contacts. At this stage those mappings will be according to users from your Drupal site. You will need to delete these mappings so that Backdrop can then automatically rebuild them from the new users table.

```
TRUNCATE TABLE `<civicrm_database>`.`civicrm_uf_match`;
```

### Update the Directory Path and URL

* Log in to Backdrop with a username that has administrator access to CiviCRM
* Go to **http://example.org/civicrm/admin/setting/updateConfigBackend?reset=1**
    * Set your base directory to _<backdrop_root>/files/_
    * Ensure your base URL is correct
    * Click **Save**

### Update the Resource URLs

Up to this stage, CiviCRM screens are missing all of its CSS formatting and images because the paths to these resources are incorrect. This step is to set the correct paths to these resources.

* Go to http://example.org/civicrm/admin/setting/url?reset=1
    * Set CiviCRM Resource URL to [civicrm.root]/
    * Set Image Upload URL to [civicrm.files]/persist/contribute/
    * Se extension Resource URL to [cms.root]/civi/extensions/
    * Click **Save**

### Rebuild the menus

The menu links in the civicrm_menu table contain full URL links and need to be updated with the new domain. Navigate to _http://example.org/civicrm/admin/menu/rebuild&reset=1_

### Review other Admin settings

As a result of your migration, many of your site's other configuration settings have been reset, for instance Available Countries in "Localization" and reCaptcha keys. Page through the _Configuration Checklist_ at **Administer > Administration Console > Configuration Checklist** to be sure that all your settings are once again set to their correct values.

### If your URL has changed

If your URL has changed, please review the documentation on [this page](/misc/switch-servers.md) under the heading "Additional Steps if your URL has changed".




## Drupal to Drupal

In most cases when moving a site, you are moving both the CMS (Drupal or Joomla!) as well as CiviCRM at the same time. If that is the case, please refer to the instructions on [Moving an Existing Installation to a New Server or Location](/misc/switch-servers.md).

This page explains how to do a rarely needed action - switching a CiviCRM installation from working with one Drupal site to working with a different Drupal site. You need good MySQL skills to attempt this. If you don't have them, it is recommended you seek [professional assistance](http://civicrm.org/professional) from a provider with technical expertise.

When would this be needed? Perhaps a Drupal shop redevelops a Drupal site without including CiviCRM integration, and the CiviCRM site on the old site needs to be (re-)attached to the new site. Or in my case, a CiviCRM site used exclusively for back-office operations needs to be added to a new Drupal site developed by a Drupal shop so that it can start to expose public facing functionality like Event registrations.

This recipe documents a Drupal 6 / CiviCRM 3.x procedure. While it is phrased as a moving from an old server with a Drupal 6 / CiviCRM installation to a new one that starts with just a Drupal 6 site, it also covers doing this on a single server. Note that it is alpha code, having worked once. Use at your own discretion.

The general approach is as follows:

* Put your new site into Maintenance mode
* Upgrade Drupal core and contrib modules that are on your old site so they match the version numbers on the new Drupal site
* Disable CiviCRM helper modules
* Move your files
* Copy your old civicrm and drupal databases to the new server
* Insert CiviCRM entries into Drupal tables that are normally done by the CiviCRM installation script, but taking the data from your old site
* Integrate the users from the two Drupal sites
* Let CiviCRM know about its new home
* Rebuild the menus
* Fix the directories
* Review other Admin settings
* Enable Helper Modules
* If your URL has changed

### Put your new site into Maintenance mode

### Upgrade Drupal core and contrib modules that are on your old site so they match the version numbers on the new Drupal site

This is to ensure there are no unexpected differences in the Drupal tables to be integrated later in the process. May not always be necessary for modules that don't affect tables used by CiviCRM or the Drupal's user / role / permissioning systems. Better safe than sorry. Of course, you may be able to modify the insert statements later to handle changes in the field structures.

### Disable CiviCRM helper modules

Disable helper modules like `civicrm_roles_sync`, but leave CiviCRM enabled.

### Move your files

1. Copy your civicrm files from your old server to your new server.
    1. This should include the codebase at `sites/all/modules` (safer to copy the existing one than to get a new tarball),
    1. any overridden php or tpl files (I store these in `sites/mysite/files/civicrm/custom_php` and `sites/mysite/files/civicrm/custom_tpl`),
    1. and other civicrm files (from `sites/mysite/files/civicrm` including `custom`, `persist`, and `upload` subdirectories).
1. Delete CiviCRM's temporary files if you copied them (ie `sites/mysite/files/civicrm/templates_c/*`), and ensure the directory exists if you didn't copy the files over.
1. Ensure the ownership and permissions on the directories and files are correct - ie readable by web server process, writable in the case of the `files/` directory excluding the overridden php and tpl dirs).
1. If you have Drupal contrib modules that interact with civicrm in your `sites/mysite/modules` or `sites/all/modules` directories, you should move them.

### Copy your old civicrm and drupal databases to the new server

1. Dump you old server's drupal and civicrm databases.
1. Copy the dump files over to the new server.
1. Restore the old civicrm database into the new server's permanent civicrm database, mysite_civ.
1. Restore the old Drupal database into a temporary drupal database on the new server, old_dru.
1. We will be integrating some of its content with the new Drupal database, mysite_dru.

### Insert entries into Drupal tables

Insert CiviCRM entries into Drupal tables that are normally done by the CiviCRM installation script, but taking the data from your old site

1. Login to your mysql instance via a command line or phpMyAdmin using a mysql account that has privileges for `old_dru`, `mysite_dru`, and `mysite_civ`.

1. As a precaution if you did not upgrade Drupal core and contrib modules to the same version, before doing any of the following operations on the bocks or other tables, compare the old and new table structures using `SHOW CREATE TABLE old_dru.blocks;` and `SHOW CREATE TABLE mysite_dru.blocks;`

1. Run this...
    ```sql
    INSERT INTO mysite_dru.blocks (
      `module`,
      `delta`, 
      `theme`, 
      `status`, 
      `weight`,
      `region`, 
      `custom`,
      `throttle`,
      `visibility`,
      `pages`,
      `title`, 
      `cache`)
    SELECT
      `module`,
      `delta`,
      `theme`,
      `status`,
      `weight`,
      `region`,
      `custom`,
      `throttle`,
      `visibility`,
      `pages`,
      `title`,
      `cache`
    FROM old_dru.`blocks`
    WHERE module = 'civicrm';
    ```

1. Run this... (the fancy stuff is to handle updating of plid)

    ```sql
    INSERT INTO mysite_dru.menu_links (
      `menu_name`,
      `plid`,
      `link_path`,
      `router_path`,
      `link_title`,
      `options`,
      `module`,
      `hidden`,
      `external`,
      `has_children`,
      `expanded`,
      `weight`,
      `depth`,
      `customized`,
      `p1`,
      `p2`,
      `p3`,
      `p4`,
      `p5`,
      `p6`,
      `p7`,
      `p8`,
      `p9`,
      `updated`)
      
    SELECT
      m1.menu_name,
      if(m3.mlid is null, 0, m3.mlid) as plid,
      m1.link_path,
      m1.`router_path`,
      m1.`link_title`,
      m1.`options`,
      m1.`module`,
      m1.`hidden`,
      m1.`external`,
      m1.`has_children`,
      m1.`expanded`,
      m1.`weight`,
      m1.`depth`,
      m1.`customized`,
      m1.`p1`,
      m1.`p2`,
      m1.`p3`,
      m1.`p4`,
      m1.`p5`,
      m1.`p6`,
      m1.`p7`,
      m1.`p8`,
      m1.`p9`,
      m1.`updated`
    FROM `old_dru`.`menu_links` m1 
    LEFT JOIN old_dru.menu_links m2 ON m1.plid=m2.mlid
    LEFT JOIN mysite_dru.menu_links m3 ON m2.router_path=m3.router_path
    WHERE
      m1.router_path like '%civicrm%' AND 
      if(m1.plid, m3.mlid, TRUE);
    ```


1. Run this...

    ```sql
    INSERT INTO mysite_dru.menu_router (
      `path`,
      `load_functions`,
      `to_arg_functions`,
      `access_callback`,
      `access_arguments`,
      `page_callback`,
      `page_arguments`,
      `fit`,
      `number_parts`,
      `tab_parent`,
      `tab_root`,
      `title`,
      `title_callback`,
      `title_arguments`,
      `type`,
      `block_callback`,
      `description`,
      `position`,
      `weight`,
      `file`) 
    SELECT 
      `path`,
      `load_functions`,
      `to_arg_functions`,
      `access_callback`,
      `access_arguments`,
      `page_callback`,
      `page_arguments`,
      `fit`,
      `number_parts`,
      `tab_parent`,
      `tab_root`,
      `title`,
      `title_callback`,
      `title_arguments`,
      `type`,
      `block_callback`,
      `description`,
      `position`,
      `weight`,
      `file`
    FROM `old_dru`.`menu_router`
    WHERE `path` LIKE '%civicrm%';
    ```

1. Check that all of the modules desired are captured - perhaps you have a module with civi but not civicrm in its name, in which case you will need to ensure it is copied over

    ```sql
    INSERT INTO mysite_dru.system (
      `filename`,
      `name`,
      `type`,
      `owner`,
      `status`,
      `throttle`,
      `bootstrap`,
      `schema_version`,
      `weight`,
      `info`)
    SELECT
      `filename`,
      `name`,
      `type`,
      `owner`,
      `status`,
      `throttle`,
      `bootstrap`,
      `schema_version`,
      `weight`,
      `info`
    FROM `old_dru`.`system`
    WHERE `name` LIKE '%civicrm%';
    ```


1. This merely inserts new roles that include Civi* perms - you may get extrras if non CiviCRM permissions have names including Civi

    ```sql
    INSERT INTO mysite_dru.role (`name`) 
    SELECT r.`name` 
    FROM role r 
    INNER JOIN permission p ON
      r.rid=p.rid AND 
      perm LIKE '%Civi%'
    LEFT JOIN mysite_dru.role rn ON r.name=rn.name
    WHERE rn.name IS NULL;
    ```

1. Manually set CiviCRM permissions on roles in new site to match those on the old site. (Okay, I'm lazy and didn't want to do this in code as permissions isn't normalized on each perm.)

1. Truncate sessions and various cache tables. Depending on your installation, you may need to truncate additional module-specific cache tables.

    ```sql
    USE mysite_dru;
    TRUNCATE `cache`;
    TRUNCATE `cache_block`;
    TRUNCATE `cache_filter`;
    TRUNCATE `cache_form`;
    TRUNCATE `cache_menu`;
    TRUNCATE `cache_page`;
    TRUNCATE `sessions`;
    ```

### Integrate the users from the two Drupal sites

1. left join used to get only new users

    ```sql
    INSERT INTO mysite_dru.users (
      `name`,
      `pass`,
      `mail`,
      `mode`,
      `sort`,
      `threshold`,
      `theme`,
      `signature`,
      `signature_format`,
      `created`,
      `access`,
      `login`,
      `status`,
      `timezone`,
      `language`,
      `picture`,
      `init`,
      `data`)
    SELECT
      ou.`name`,
      ou.`pass`,
      ou.`mail`,
      ou.`mode`,
      ou.`sort`,
      ou.`threshold`,
      ou.`theme`,
      ou.`signature`,
      ou.`signature_format`,
      ou.`created`,
      ou.`access`,
      ou.`login`,
      ou.`status`,
      ou.`timezone`,
      ou.`language`,
      ou.`picture`,
      ou.`init`,
      ou.`data`
    FROM `users` ou 
    LEFT JOIN mysite_dru.users nu ON ou.mail=nu.mail 
    WHERE nu.mail IS NULL;
    ```

1. Run this...

    ```sql
    INSERT INTO mysite_dru.users_roles (
      uid,
      rid) 
    SELECT
      nu.uid,
      nr.rid 
    FROM old_dru.users ou
    INNER JOIN mysite_dru.users nu ON ou.mail=nu.mail
    INNER JOIN old_dru.users_roles our ON ou.uid=our.uid
    INNER JOIN old_dru.role oldr ON our.rid=oldr.rid
    INNER JOIN mysite_dru.role nr ON oldr.name=nr.name
    LEFT JOIN mysite_dru.users_roles nur ON 
      our.uid=nur.uid AND 
      our.rid=nur.rid
    WHERE nur.rid IS NULL
    ```

1. Run this...

    ```sql
    UPDATE civicaction_civ.`civicrm_uf_match` m
    INNER JOIN civicaction_dru.users u ON
      (m.uf_name COLLATE utf8_unicode_ci) = (u.mail COLLATE utf8_unicode_ci)
    SET uf_id = uid
    ```

### Let CiviCRM know about its new home

1. Login as user 1 and navigate to /index.php?q=civicrm/admin/setting/updateConfigBackend&reset=1
1. Review the recommended modified paths in the form - they should reflect the new Base Directory, Base URL, and Site name for CiviCRM.

### Rebuild the menus

The menu links in the civicrm_menu table contain full URL links and need to be updated with the new domain. Navigate to _http://example.org/index.php?q=civicrm/menu/rebuild&reset=1_

### Fix the directories

1. Go to **Administer > System Settings**. If you are using custom templates or PHP then you'll need to click on **Directories** to update the directory locations for these (/civicrm/admin/setting/path?reset=1). You should click on **Resource URLs** to ensure these are correct.

### Review other Admin settings

1. As a result of your migration, many of your site's other configuration settings have been reset, for instance Available Countries in "Localization" and reCaptcha keys. Page through the _Configuration Checklist_ at **Administer > Administration Console > Configuration Checklist** to be sure that all your settings are once again set to their correct values.

### Enable Helper Modules

If you disabled helper modules like civicrm_members_sync, time to turn them back on.

### If your URL has changed

If your URL has changed, please review the documentation on [this page](/misc/switch-servers.md) under the heading "Additional Steps if your URL has changed".
