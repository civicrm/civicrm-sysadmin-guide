# Moving CiviCRM from Joomla to Drupal

## Preparing for the migration

* Backup your system
    * Backup the CiviCRM database that was set up for Joomla.
    * Backup the Drupal site before making any changes to it.

        !!! tip
            If you are going to be importing and overwriting your older CiviCRM database over the newly installed, blank CiviCRM database, adding IF NOT EXISTS clauses to the CREATE TABLE statements will help the import. DROP TABLE statements will fail because of foreign key restraints.

    * Set up CiviCRM in your Drupal site

        !!! check
            When downloading the CiviCRM package get the same version of the CiviCRM package for Drupal as the version you used in Joomla. If you want to upgrade to a newer version of CiviCRM, upgrade it before or after – not during – the migration.

        Refer to the [Drupal Installation Guide](/install/drupal7.md) for details on obtaining and setting up CiviCRM for Drupal.

## Restore the CiviCRM database backup to the desired location

* The installation process would have set up new database tables for CiviCRM. If this is where you want your CiviCRM database to be, then you may overwrite these tables by restoring your old database over it.
* Alternatively you can restore your CiviCRM database somewhere else and modify **CIVICRM_DSN** in _<drupal_root>/sites/default/civicrm.settings.php_ to point to this new database.

## Empty the `civicrm_uf_match` table

This table is used for mapping your CMS users with CiviCRM contacts. At this stage those mappings will be according to users from your Joomla site. You will need to delete these mappings so that Drupal can then automatically rebuild them from the new users table.

```
TRUNCATE TABLE `<civicrm_database>`.`civicrm_uf_match`;
```

## Update the Directory Path and URL

* Log in to Drupal with a username that has administrator access to CiviCRM
* Go to **http://example.org/civicrm/admin/setting/updateConfigBackend?reset=1**
    * Set your base directory to _<drupal_root>/sites/default/files/_
    * Ensure your base URL is correct
    * Click **Save**

## Update the Resource URLs

Up to this stage, CiviCRM screens are missing all of its CSS formatting and images because the paths to these resources are incorrect. This step is to set the correct paths to these resources.

* Go to **http://example.org/civicrm/admin/setting/url?reset=1**
    * Set CiviCRM Resource URL to _http://example.org/sites/all/modules/civicrm/_
    * Set Image Upload URL to _http://example.org/sites/default/files/civicrm/persist/contribute/_
    * Click **Save**

## Rebuild the menus and triggers

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

## Drupal table prefixes

If you have table prefixes on your Drupal database you'll also need to do the following to allow CiviMail cronjobs to run, etc.

* Go to **http://example.org/civicrm/admin/setting/uf?reset=1**
    * Update Drupal Users Table Name with your_table_prefix_users (eg. drupal_users)
    * Click **Save**

## Update screen editor

If you're having trouble editing and/or adding users, check your WYSIWYG setting under Display Preferences

* Go to **http://example.org/civicrm/admin/setting/preferences/display&reset=1**
* Check the WYSIWYG editor setting and choose one of the selections (this resets to a Drupal choice vs. Joomla)
* Click **Save**

!!! check
    The CiviCRM sidebar may disappear at this point. This is because in Joomla the sidebar is fixed. In Drupal you are able to set the position of these components as **blocks** on the page.


!!! tip
    You may find the CiviCRM screens do not fit comfortably on the page if you are using a fixed width Drupal theme. A solution is to use the [civicrm_theme](http://drupal.org/project/civicrm_theme) module that enables you to choose a different theme for the CiviCRM screens.