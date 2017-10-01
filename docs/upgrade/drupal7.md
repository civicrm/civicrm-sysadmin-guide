# Upgrading CiviCRM for Drupal 7

Use this document to upgrade CiviCRM installations on Drupal 7 to the latest CiviCRM release.

!!! caution "Before upgrading"
    Make sure you have done the steps listed in ["Before upgrading"](/upgrade/index.md#before-upgrading) first.

## Download the latest code

1. Go to [civicrm.org/download](https://civicrm.org/download)
1. Select the latest currently available CiviCRM tarball for Drupal.

    Example: `civicrm-4.7.20-drupal.tar.gz`
    
1. Save this file in `<drupal_root>/sites/all/modules`.

If using localization, also download the latest version of the localization files. See the [CiviCRM Localisation](http://wiki.civicrm.org/confluence/display/CRMDOC40/CiviCRM+Localisation) page about how to install files for running CiviCRM in languages other than American English.

## Prepare Drupal

### Take the site offline

When upgrading a production site, it is recommended that you take your site offline during the upgrade process.
**Administration » Configuration » Development » Maintenance mode**

### Ensure that CiviCRM is not your Drupal homepage

Due to changes in Dashboard files from 3.0 to 3.1, you need to ensure that your Drupal homepage is not the CiviCRM homepage. If your site does use CiviCRM as its front page, change it by clicking on:
**Administration » Configuration » System » Site Information**
 Then ensure that the "Default front page" value is not civicrm (change it to node if necessary). If you made a change, click Save configuration

!!! warning
    There are a variety of modules that may affect the Drupal homepage. Consult the appropriate module's documentation if you are using them to set the homepage to civicrm for the administrative action required to change to a different non-CiviCRM homepage for the upgrade process.

### Disable all CiviCRM integration modules

If you have Drupal modules installed in your site that integrate with or extend CiviCRM's functionality, you should disable them prior to running the upgrade script. This will prevent modules that are not compatible with the new version from triggering errors in the upgrade process.

1. In Drupal, go to **Administration » Modules**

1. Note which modules in the CiviCRM section of the modules listing are currently enabled (taking a screenshot is one easy way to do this). Now un-check the Enabled box for ALL modules in the CiviCRM section of the modules listing _except_ for CiviCRM itself. Click Save.

    !!! warning
        Do NOT disable CiviCRM itself. Only disable other modules in the CiviCRM section of the modules page. The upgrade will NOT run if CiviCRM is disabled.

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

### Clear cached files

Delete all files in `sites/default/files/civicrm/templates_c/`

## Upgrade the database

Choose one of the following methods:

* Run the following `drush` command from within your site:

    ```bash
    $ drush civicrm-upgrade-db 
    ```

* Or, point your web browser to the following URL and follow the on-screen instructions.
    
    ```
    http://<your_drupal_home>/civicrm/upgrade?reset=1
    ```
    
    !!! note
        This page loads on a drupal maintenance page. If you use a custom `maintenance.tpl.php` file you may wish to temporarily disable that file and use Drupal's default.
    

## Clean up

### Re-enable compatible CiviCRM integration modules

1. In Drupal, go to **Administration » Modules**

1. Go through your list of CiviCRM integration modules and confirm compatibility with the new release if applicable. (If you've downloaded the module from Drupal.org - check the module's page for compatibility information.)

1. Re-enable all compatible modules by re-checking the Enabled box. You may want to do this "one at a time" if you're unsure of compatibility.

1. If you wish to use a different theme for CiviCRM administrative pages than the public pages on your site, enable the **CiviCRM Theme** module that now is included and then set the theme at `admin/appearance`.

### Review and update Drupal permissions settings

It's a good idea to verify Drupal role-based permissions which were added in recent releases. You can review and update these at **Administer CiviCRM » Users and Permissions » Permissions (Access Control) » Drupal Access Control**:

### Clear the views cache

If you were using Views integration prior to this upgrade, you will need to go to **Administration » Structure » Views » Settings » Advanced** and press "Clear Views cache" for Views to capture changes in the CiviCRM Views integration code.

### Restore CiviCRM as the Drupal homepage if appropriate

If you changed the default Drupal homepage from CiviCRM, now is the time to restore it. You can do this by navigating to:
**Administration » Configuration » System » Site Information**
 then change the Default front page to civicrm, and click Save configuration.

### Put the site online

Toggle the following feature, and put your site back online:

**Administration » Configuration » Development » Site Maintenance**

## Post upgrade

See these steps to take [after upgrading](/upgrade/index.md#after-upgrading).
