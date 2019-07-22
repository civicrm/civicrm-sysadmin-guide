# Drupal to Backdrop

For some use cases, instead of a Drupal 7 to Drupal 8 migration, a migration from Drupal 7 to Backdrop is a great alternative.

!!! Migrating Drupal 7 CMS to Backdrop CMS
        For information about migrating to Backdrop from Drupal, see [Upgrading from Drupal 7](https://backdropcms.org/upgrade-from-drupal) on the Backdrop website.

## Preparing for the Migration

* Backup the CiviCRM database that was set up for Drupal.
* Backup the Backdrop site before making any changes to it.
* Set up CiviCRM in your Backdrop site

    !!! check
            When downloading the CiviCRM package get the same version of the CiviCRM package for Backdrop as the version you used in Drupal. If you want to upgrade to a newer version of CiviCRM, upgrade it before or after – not during – the migration.

    Refer to the [Backdrop Installation Guide](install/backdrop.md) for details on obtaining and setting up CiviCRM for Backdrop.

## Relocate Custom Files

* Copy any extension directories from the previous Drupal directory to the new Backdrop directory at `<backdrop_root>/files/civicrm/ext`
* Copy any sensitive uploads/attachments from the previous Drupal directory to the new Backdrop directory at `<backdrop_root>/files/civicrm/custom`
* Copy any template overrides from the previous Drupal directory to the new Backdrop directory at `<backdrop_root>/files/civicrm/templates`
* Copy any uploaded media from the previous Drupal directory to the new Backdrop directory at `<backdrop_root>/files/civicrm/persist`

## Restore the CiviCRM database backup to the desired location

* The installation process would have set up new database tables for CiviCRM. If this is where you want your CiviCRM database to be, then you may overwrite these tables by restoring your old database over it.
* Alternatively you can restore your CiviCRM database somewhere else and modify **CIVICRM_DSN** in `<backdrop_root>/civicrm.settings.php` to point to this new database.

## Empty the `civicrm_uf_match` table

This table is used for mapping your CMS users with CiviCRM contacts. At this stage those mappings will be according to users from your Drupal site. You will need to delete these mappings so that Backdrop can then automatically rebuild them from the new users table.

``` sql
TRUNCATE TABLE `<civicrm_database>`.`civicrm_uf_match`;
```

## Update the Directory Path and URL

* Log in to Backdrop with a username that has administrator access to CiviCRM
* Go to **http://example.org/civicrm/admin/setting/updateConfigBackend?reset=1**
    * Set your base directory to `<backdrop_root>/files/`
    * Ensure your base URL is correct
    * Click **Save**

## Update the Resource URLs

Up to this stage, CiviCRM screens are missing all of its CSS formatting and images because the paths to these resources are incorrect. This step is to set the correct paths to these resources.

* Go to http://example.org/civicrm/admin/setting/url?reset=1
    * Set CiviCRM Resource URL to `[civicrm.root]/`
    * Set Image Upload URL to `[civicrm.files]/persist/contribute/`
    * Set extension Resource URL to `[cms.root]/civi/extensions/`
    * Click **Save**

## Rebuild the menus

The menu links in the civicrm_menu table contain full URL links and need to be updated with the new domain. Navigate to _http://example.org/civicrm/admin/menu/rebuild&reset=1_

## Review other Admin settings

As a result of your migration, many of your site's other configuration settings have been reset, for instance Available Countries in "Localization" and reCaptcha keys. Page through the _Configuration Checklist_ at **Administer > Administration Console > Configuration Checklist** to be sure that all your settings are once again set to their correct values.

## If your URL has changed

If your URL has changed, please review the documentation on [this page](/misc/switch-servers.md) under the heading "Additional Steps if your URL has changed".
