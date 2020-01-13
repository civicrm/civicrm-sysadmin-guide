# Moving CiviCRM from Drupal to WordPress

## Preparing for the migration

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

## Restore the CiviCRM database backup to the desired location

* The installation process would have set up new database tables for CiviCRM. If this is where you want your CiviCRM database to be, then you may overwrite these tables by restoring your old database over it.
* Alternatively you can restore your CiviCRM database somewhere else and modify **CIVICRM_DSN** in _<drupal_root>/sites/default/civicrm.settings.php_ to point to this new database.

!!! warning "Import failure issues"

It's possible that the import will fail due to the following error :

> Cannot delete or update a parent row: a foreign key constraint fails.

You can modify your sql import script to include (at the start):

``` sql
SET FOREIGN_KEY_CHECKS=0;
```
and the following at the very end:

``` sql
SET FOREIGN_KEY_CHECKS=1;
```

This disables the foreign key checks and allows all your tables to properly be recreated and the data to be imported. This check is then re-enabled at the end of the script.

## Empty the `civicrm_uf_match` table

This table is used for mapping your CMS users with CiviCRM contacts. At this stage those mappings will be according to users from your Drupal site. You will need to delete these mappings so that WordPress can then automatically rebuild them from the new users table.

``` sql
TRUNCATE TABLE `<civicrm_database>`.`civicrm_uf_match`;
```

## Relocate Custom Files

* On your old site, visit Administer > System Settings > Directories and Administer > System Settings > Resource URL's.
* Copy all of your custom files and uploads to the appropriate directories on your new site.
 In WordPress, these will likely be under wp-content/plugins/files/civicrm/civicrm
 In drupal, these will likeley be under sites/default/files/civicrm.


## Update the Base Directory Path and URL

* Log in to WordPress with a username that has administrator access to CiviCRM
* Go to _**http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/updateConfigBackend&reset=1**_
    * Set your base directory to _<wordpress_root>/wp-content/plugins/files/_
    Ensure your base URL is correct - it should be _http://example.org/_ (the main URL of your site)
    * Click **Save**

## Update the Resource URLs

Up to this stage, CiviCRM screens are missing all of its CSS formatting and images because the paths to these resources are incorrect. This step is to set the correct paths to these resources.

* Go to _**http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/url&reset=1**_
    * Set CiviCRM Resource URL to _http://example.org/wp-content/plugins/civicrm/civicrm/_
    * Set Image Upload URL to _http://example.org/wp-content/plugins/files/civicrm/persist/contribute/_ (may need to be created)
    * Click **Save**

## Rebuild the menus

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

## WordPress table prefixes

If you have table prefixes on your Drupal database you'll also need to do the following to allow CiviMail cronjobs to run, etc.

* Go to **http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/admin/setting/uf&reset=1**
    * Update WordPress Users Table Name with your_table_prefix_users (eg. `wp_users`) You may have changed this in accordance with WordPress best practices to have a different prefix other than `wp_`.
    * Click **Save**

## Update the changed URL in your user data

When switching CMSes, your URLs will ALWAYS be changed. Please review the documentation on [this page](/misc/switch-servers.md) under the heading "Additional Steps if your URL has changed".

## Problems

If you have a DBA with a solid understanding of CiviCRM you can manually migrate tables leaving out ACL tables, cache tables, and any tables which don't have data, but audit your migration thoroughly before opening for business. Do not add any data until it has been satisfactorily audited to confirm data is complete.
