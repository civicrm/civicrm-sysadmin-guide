# Upgrading CiviCRM for Drupal 7

Use this document to upgrade CiviCRM installations on Drupal 7 to the latest CiviCRM release.

!!! caution "Before upgrading"
    Make sure you have done the steps listed in ["Before upgrading"](/upgrade/index.md#before-upgrading) first.

## Download the latest code

1. Go to [civicrm.org/download](https://civicrm.org/download)
1. Select the latest currently available CiviCRM tarball for Drupal.

    Example: `civicrm-x.x.x-drupal.tar.gz`
    
1. Save this file in `<drupal_root>/sites/all/modules`.

If using localization, also download the latest version of the localization files. See the [CiviCRM Localisation](https://wiki.civicrm.org/confluence/display/CRMDOC/i18n+Administrator%27s+Guide%3A+Using+CiviCRM+in+your+own+language) page about how to install files for running CiviCRM in languages other than American English.

## Prepare Drupal

### Take the site offline

When upgrading a production site, it is recommended that you take your site offline during the upgrade process.
**Administration » Configuration » Development » Maintenance mode**. Taking the site offline is especially recommended if your site is publicly accessible, for example to accept donations or email signups, since any actions being taken while the upgrade is in progress may fail, and additionally if the site is in use during the upgrade, the database may become corrupt.

!!! warning
    Sometimes Drupal modules, paticularly those which integrate with CiviCRM, are not compatible with the latest version of CiviCRM. In this case the update to CiviCRM may trigger errors. Although this problem is rare, unless you are confident and about handling such errors, the safest workflow is to disable all Drupal modules which integrate with CiviCRM before performing the upgrade, and to re-enable them after the upgrade is complete.
    
## Upgrade the filesystem

### Delete all previous version code files

CiviCRM will not run properly if files from previous version are present after the upgrade.

1. Make sure you have a good backup of your complete previous version installation

    !!! warning
        Be sure that your backup copy of the previous version codebase is _not_ located below the `<drupal_root>/modules` or `<drupal_root>/sites/all/modules` directory. For example, placing your codebase backup in `<drupal_root>/modules/civicrm.bak` ... will cause errors (Drupal executes any `*.module` files that are found in the modules or `sites/all/modules` directory tree).

1. Make sure you have logged in to Drupal _before_ deleting the old files. Do not log out until the entire process is complete.

1. Delete `/sites/all/modules/civicrm`.

### Unpack the latest package

Unpack the new files into `<drupal_root>/sites/all/modules/`.

```
$ cd <drupal_root>/sites/all/modules
$ tar -xzf <civicrm_download_file>.tgz
```

You should now have a new civicrm directory tree under the `<drupal_root>/sites/all/modules/` directory.

### Verify permissions

Ensure that your Drupal `files` directory is writeable by the webserver.

```
$ cd <drupal_root>/sites/default/files
$ chown -R www-data: .
$ find . -type d -exec chmod 0755 {} \;
$ find . -type f -exec chmod 0644 {} \;
$ chmod -R u+rw files
```

## Upgrade the database

Choose one of the following methods:

* Run the following `drush` command from within your site:

    ```bash
    $ drush civicrm-upgrade-db 
    ```
    
* Or, point your web browser to the following URL and follow the on-screen instructions.
    
    ```
    http://example.org/civicrm/upgrade?reset=1
    ```
    
* Or, run the following `cv` command from within your site:

    ```bash
    $ cv upgrade:db 
    ```
    
!!! note
    The `cv upgrade:db` command accepts a --dry-run option to show what changes will be made before committing them to the database. 
    For the maximum level of verbosity use `cv upgrade:db --dry-run -vv`.
    

## Clear cached files¶

Delete all files in sites/default/files/civicrm/templates_c/, or clear the caches using one of the command line tools, such s `cv flush` or `drush cache-clear civicrm`.

    
!!! note
    If you disabled modules, or placed the site in maintenance mode, remember to reverse those steps after the upgrade.
