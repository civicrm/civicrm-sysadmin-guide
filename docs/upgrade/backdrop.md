# Upgrading CiviCRM for Backdrop CMS

Use this document to upgrade CiviCRM installations on Backdrop CMS to the latest CiviCRM release.

!!! caution "Before upgrading"
    Make sure you have done the steps listed in ["Before upgrading"](/upgrade/index.md#before-upgrading) first.

## Download the latest code

1. Go to [civicrm.org/download](https://civicrm.org/download)
1. Select the latest currently available CiviCRM tarball for Backdrop.

    Example: `civicrm-x.x.x-backdrop.tar.gz`
   
1. Save this file in `<backdrop_root>/modules`.

If using localization, also download the latest version of the localization files. See the [CiviCRM Localisation](https://wiki.civicrm.org/confluence/display/CRMDOC/i18n+Administrator%27s+Guide%3A+Using+CiviCRM+in+your+own+language) page about how to install files for running CiviCRM in languages other than American English.

## Prepare Backdrop

### Take the site offline

When upgrading a production site, it is recommended that you take your site offline during the upgrade process.
**Administration » Configuration » Development » Maintenance mode**

### Ensure that CiviCRM is not your Backdrop homepage

Due to changes in Dashboard files from 3.0 to 3.1, you need to ensure that your Backdrop homepage is not the CiviCRM homepage. If your site does use CiviCRM as its front page, change it by clicking on:
**Administration » Configuration » System » Site Information**
 Then ensure that the "Default front page" value is not civicrm (change it to node if necessary). If you made a change, click Save configuration

!!! warning
    There are a variety of modules that may affect the Backdrop homepage. Consult the appropriate module's documentation if you are using them to set the homepage to civicrm for the administrative action required to change to a different non-CiviCRM homepage for the upgrade process.

### Disable all CiviCRM integration modules

If you have Backdrop modules installed in your site that integrate with or extend CiviCRM's functionality, you should disable them prior to running the upgrade script. This will prevent modules that are not compatible with the new version from triggering errors in the upgrade process.

1. In Backdrop, go to **Administration » Functionality**

1. Note which modules in the CiviCRM section of the modules listing are currently enabled (taking a screenshot is one easy way to do this). Now un-check the Enabled box for ALL modules in the CiviCRM section of the modules listing _except_ for CiviCRM itself. Click Save.

    !!! warning
        Do NOT disable CiviCRM itself. Only disable other modules in the CiviCRM section of the modules page. The upgrade will NOT run if CiviCRM is disabled.

## Upgrade the filesystem

### Delete all previous version code files

CiviCRM will not run properly if files from previous version are present after the upgrade.

1. Make sure you have a good backup of your complete previous version installation

    !!! warning
        Be sure that your backup copy of the previous version codebase is _not_ located below the `<backdrop_root>/modules` directory. For example, placing your codebase backup in `<backdrop_root>/modules/civicrm.bak` ... will cause errors (Backdrop executes any `*.module` files that are found in the modules or `/modules` directory tree).

1. Make sure you have logged in to Backdrop _before_ deleting the old files. Do not log out until the entire process is complete.

1. Delete `/modules/civicrm`.

### Unpack the latest package

Unpack the new files into `<backdrop_root>/modules/`.

```
$ cd <backdrop_root>/modules
$ tar -xzf <civicrm_download_file>.tgz
```

You should now have a new civicrm directory tree under the `<backdrop_root>/modules/` directory.

### Verify permissions

Ensure that your Backdrop `files` directory is writeable by the webserver.

```
$ cd <backdrop_root>/files
$ chown -R www-data: .
$ find . -type d -exec chmod 0755 {} \;
$ find . -type f -exec chmod 0644 {} \;
$ chmod -R u+rw files
```

### Clear cached files

Delete all files in `files/civicrm/templates_c/`

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
   
    !!! note
        This page loads on a Backdrop maintenance page. If you use a custom `maintenance.tpl.php` file you may wish to temporarily disable that file and use Backdrop's default.
   

## Clean up

### Re-enable compatible CiviCRM integration modules

1. In Backdrop, go to **Administration » Functionality**

1. Go through your list of CiviCRM integration modules and confirm compatibility with the new release if applicable. (If you've downloaded the module from backdropcms.org - check the module's page for compatibility information.)

1. Re-enable all compatible modules by re-checking the Enabled box. You may want to do this "one at a time" if you're unsure of compatibility.

1. If you wish to use a different theme for CiviCRM administrative pages than the public pages on your site, enable the **CiviCRM Theme** module that now is included and then set the theme at `admin/appearance`.

### Review and update Backdrop permissions settings

It's a good idea to verify Backdrop role-based permissions which were added in recent releases. You can review and update these at **Administer CiviCRM » Users and Permissions » Permissions (Access Control) » Backdrop Access Control**:

### Clear the views cache

If you were using Views integration prior to this upgrade, you will need to go to **Administration » Structure » Views » Settings » Advanced** and press "Clear Views cache" for Views to capture changes in the CiviCRM Views integration code.

### Restore CiviCRM as the Backdrop homepage if appropriate

If you changed the default Backdrop homepage from CiviCRM, now is the time to restore it. You can do this by navigating to:
**Administration » Configuration » System » Site Information**
 then change the Default front page to civicrm, and click Save configuration.

### Put the site online

Toggle the following feature, and put your site back online:

**Administration » Configuration » Development » Site Maintenance**

## Post upgrade

See these steps to take [after upgrading](/upgrade/index.md#after-upgrading).
