# Upgrading CiviCRM for WordPress

Use this document to upgrade CiviCRM installations on WordPress to the latest CiviCRM release.

!!! caution "Before upgrading"
    Make sure you have done the steps listed in ["Before upgrading"](upgrade/index.md#before-upgrading) first.

## Download the latest code

1. Go to [civicrm.org/download](https://civicrm.org/download)
1. Select the latest currently available CiviCRM tarball for WordPress.

    Example: `civicrm-x.x.x-wordpress.zip`
   
1. Save this file in `<wordpress_root>/wp-content/plugins/`.

If using localization, also download the latest version of the localization files. See the [CiviCRM Localisation](https://wiki.civicrm.org/confluence/display/CRMDOC/i18n+Administrator%27s+Guide%3A+Using+CiviCRM+in+your+own+language) page about how to install files for running CiviCRM in languages other than American English.


## Prepare WordPress

Make sure you have logged in to WordPress as an administrator. Do not log out until the entire process is complete.

## Backup
### Settings files
#### civicrm.settings.php

The ["Before upgrading"](upgrade/index.md#before-upgrading) steps describe steps for backing _everything_ up in case something goes wrong during the upgrade. In addition to this important safeguard, we also need to actually _use_ the `civicrm.settings.php` settings file, as it is, during the upgrade.

!!! note For **CiviCRM 4.6** and older:
    `<wordpress_root>/wp-content/plugins/civicrm/civicrm.settings.php`

!!! note For **CiviCRM 4.7+**:
    `<wordpress_root>/content-dir/uploads/civicrm/civicrm.settings.php`

    *The installer in 4.7 and above does not assume that the content-dir is wp-content. It probably is, but can be renamed/moved as detailed [here](https://codex.wordpress.org/Editing_wp-config.php#Moving_wp-content_folder)*
   
1. Copy this file to a location outside your WordPress project. You may need to restore it after upgrading.
1. Also, if you are using the `<wordpress_root>/wp-content/plugins/civicrm/civicrm/settings_location.php` file in your implementation, make a copy of this as well as you will need to restore it after upgrading.

#### CiviCRM files and database
1. Backup your existing <wordpress>/wp-content/plugins/civicrm directory by creating an archive or FTPing it to your local computer.

!!! note: It's important at the very least to backup your extensions directory prior to the upgrade. This will be removed in the next few steps. Your extensions folder can be found at **Administer menu » System Settings » Directories**. Make a note of this location for later.

1. Backup your existing database using your Control Panel or other method so that you can recreate the database if required.

## Now upgrade the filesystem

### Remove old CiviCRM files

CiviCRM will not run properly if files from previous version are present after the upgrade.

1. Make sure you have a good backup of your complete previous version installation
1. Delete `<wordpress_root>/wp-content/plugins/civicrm`

### Install new CiviCRM files

1. Unpack the files.

    ```bash
    $ cd <wordpress_root>/<content-dir>/plugins
    $ unzip <civicrm_download_file>.zip
    ```

!!! caution **Upgrading from CiviCRM 4.1**:
    If you have upgraded from a CiviCRM 4.1 version, check the contents of `civicrm.settings.php`. Make sure at the very end of the file these 2 lines exist, and if not, add them.
    ```php
    require_once 'CRM/Core/ClassLoader.php';
    CRM_Core_ClassLoader::singleton()->register();
    ```

!!! note For **CiviCRM 4.6** and older:
    For Versions installed prior to 4.7 - Restore the original `civicrm.settings.php` file from your backup into the `<wordpress_root>/wp-content/plugins/civicrm/` directory.

1. If you are using the `<wordpress_root>/<content-dir>/plugins/civicrm/civicrm/settings_location.php` file in your implementation, restore this file from your back-up.

### Install localization files (optional)

If your site requires the localization files, download the `civicrm-x.x.x-l10n.tar.gz` from [civicrm.org/download](https://civicrm.org/download)

These files must be untarred under `<wordpress_root>/wp-content/plugins/civicrm/`. Please note the `tar.gz` file only contains `/civicrm` not `/civicrm/civicrm`

```
$ cd <wordpress_root>/<content-dir>/plugins/civicrm
$ tar -xf ../civicrm-x.x.x-l10n.tar.gz
```

If you don't have SSH access and your IP panel does not recognize tar files, you will need to convert it to a zip file.

1. Uncompress `civicrm-x.x.x-l10n.tar.gz`
1. Uncompress `civicrm-x.x.x-l10n.tar`. You should now have a `civicrm` directory with 2 subdirectories `l10n` and `sql`.
1. Rename `civicrm` to `civicrm2`.
1. Create an empty `civicrm` directory.
1. Move the `civicrm2` directory (with the 2 subdirectories) into your new `civicrm` directory.
1. Rename `civicrm2` to `civicrm`.
1. Zip the `civicrm` directory and name it `civicrm-x.x.x-l10n.zip`
1. Now, you can upload `civicrm-x.x.x-l10n.zip` into `<wordpress_root>/wp-content/plugins/`
1. Unzip `civicrm-x.x.x-l10n.zip` and continue with the following steps.


### Clear cached files

Delete all files in your `templates_c` directory

* In CiviCRM 4.7 and above:
    `<wordpress_root>/<content-dir>/uploads/civicrm/templates_c`
   
!!! caution "For CiviCRM 4.6 and older:"
    `<wordpress_root>/<content-dir>/plugins/files/civicrm/templates_c`

## Copy back your extensions
1. Restore the extensions that were previously backed up into the folder where you originally backed up your extensions at the start of the upgrade process.

## Upgrade the database

1. Point your web browser to the following URL

    ```
    http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/upgrade&reset=1
    ```

1. You should see the Upgrade screen.
1. If you are ready to upgrade, click the **Upgrade Now** button.
1. You should see the message **Upgrade successful** when the upgrade completes.
    * If you receive any errors during the process, please note down the exact error message, and check for solutions on [Stack Exchange](https://civicrm.stackexchange.com/) or [Mattermost](https://chat.civicrm.org).
   
1. Now click the **Return to CiviCRM home page** link. This will rebuild CiviCRM menus automatically and return you to the CiviCRM home dashboard.


## Post upgrade

See these steps to take [after upgrading](upgrade/index.md#after-upgrading).
