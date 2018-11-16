# Installing CiviCRM for Backdrop

## Scope of this guide and alternative installation methods

This guide covers standard installation of CiviCRM for production use. For installing a development environment, refer to the [section on Buildkit in the Developer Documentation](https://docs.civicrm.org/dev/en/latest/tools/buildkit/).

## Before installing

1. Ensure that your system meets the [requirements](/requirements.md).
1. Install Backdrop by referring to the [Backdrop Installation Guide](http://backdropcms.org/getting-started) if needed.

## Determine Backdrop Database Settings {:#db-settings}

You will need to know the database settings for your Backdrop installation prior to running the CiviCRM installer: You can look up these values in your Backdrop `settings.php` file (located in your Backdropp root) be looking for the following code:

```
$db_url = 'mysql://dbuser:dbpassword@localhost/backdrop';
```

or

```
$databases['default']['default'] = array(
  'driver' => 'mysql',
  'database' => 'database_name',
  'username' => 'dbuser',
  'password' => 'dbpassword',
  'host' => 'localhost',
);
```

In the above example:

| Setting | Value |
| --- | --- |
| Database Server | localhost |
| Backdrop Database Name | backdrop |
| Database User Name | dbuser |
| Database User Password | dbpassword |

## Tell Backdrop where to find the CiviCRM Module {:#directory}

First, download CiviCRM and install the files like you would any other module:

* As administrator in Backdrop, go to Modules, click on **Install New Module**.  Click **Manual Installation**, then **Install from a URL**.  Fill in the URL of the module (see below). You will need to enable the **Project Installer** module in the backdrop modules page to see the **Install New Module** link.
* Alternatively, you can upload and extract the file in `<BACKDROP ROOT>/modules`

The result will be that the CiviCRM directory will be at `<BACKDROP ROOT>/modules/civicrm`

The most up-to-date version of CiviCRM will always be available at [https://civicrm.org/download](https://civicrm.org/download).

**Do NOT** proceed to "activate" the module - that will happen automatically when you run the installer.

## Install localization files (only for non-English sites) {:#i18n}

If using CiviCRM in another language than English, see the [internationalisation and localisation](https://wiki.civicrm.org/confluence/display/CRMDOC/i18n+Administrator%27s+Guide%3A+Using+CiviCRM+in+your+own+language) page about how to install files for running CiviCRM in other languages.

## Run the Installer {:#installer}

The installer will verify that you've downloaded the correct version of CiviCRM, and will check your server environment to make sure it meets CiviCRM requirements. It will then create and populate a database for CiviCRM as well as create your CiviCRM settings file (civicrm.settings.php).

* Login to your Backdrop site with Administrator level permissions.
* Point your web browser to the following URL:

    `http://example.org/modules/civicrm/install/index.php?civicrm_install_type=backdrop`
    
* You should see the **CiviCRM Installer** screen.
    * Initially, you will see a red bar with the message "These database details don't appear to be correct." This is expected as you haven't entered your database settings yet.
    * If you see other errors, check the **Requirements** details at the bottom of the page for more information. You will need to correct any issues before continuing.
* Fill in the CiviCRM Database Settings.

    !!! tip "Where Should I Store CiviCRM Data?"
         CiviCRM may be configured to use your existing Backdrop database, or a separate (new) database. Using a separate database is generally preferred - as it makes backups and upgrades easier. The installer will create a new database for CiviCRM automatically if you enter a database name that doesn't already exist on your database server AND the database user you enter has permission to create databases. In case the installer does not automatically create a new database, simply create a new one following the same process as creating a new database for Backdrop. Note that if you plan to use the Backdrop Views module to display CiviCRM data within your Backdrop pages, and if you are going to use separate databases for Backdrop and CiviCRM, you need to ensure that your Backdrop database user has `SELECT` permissions for your CiviCRM database.

* Fill in the Backdrop Database Settings for your existing Backdrop database (as noted [above](#db-settings)).

    !!! check "Loading Sample Data"
        The Installer includes an option to load a set of sample contact, group, and relationship data by default. Sample data can provide a useful head-start in learning to use CiviCRM. However, if you do NOT want the sample data to be loaded, just uncheck **Load sample data** under **Other Settings**.

* Select the appropriate language for the base installation. You will be able to add other languages after the installation for multi-lingual sites.
* Click the **Check Requirements and Install CiviCRM** button.
    * The installer will configure your databases, create the settings file and redirect you to your Backdrop Home page.
    * If you still see a red bar with the message "These database details don't appear to be correct." - check the Database Details section below your settings for specific errors and problems. Once you correct these problems, click "Recheck requirements" to verify your settings before continuing.
    * If you are on a Windows machine and get the message "The user account used by your web-server needs to be granted write access to the following directory in order to configure the CiviCRM settings file: C:<BACKDROP ROOT>/" even after changing directory permission in Explorer, see [http://forum.civicrm.org/index.php/topic,5056.msg23720.html#msg23720](http://forum.civicrm.org/index.php/topic,5056.msg23720.html#msg23720) for instructions how to change the permissions using CMD.
    * Once you see the green "You're ready to install!" message - you can click **Check Requirements and Install CiviCRM**

## Review Permissions {:#permissions}

!!! check ""

    Note that Backdrop tries to create the `/files/` directory (and make it writeable), but only when saving `admin/settings`. Same holds for `/temp` directory, and a `/uploads/` directory in the CiviCRM module root. On a brand-new Backdrop install, this directory may be missing. Even on an existing installation, if file permissions are not set properly, the directory may be missing. If enabling the **civicrm** module generates errors regarding the files directory, you must create it (writeable) manually.

* Go to **Administer » Configuration » User accounts » Permissions**

* Verify that the Roles that you want to have access to CiviCRM have the appropriate permissions checked. CiviCRM is installed with a number of fixed permissions (such as "edit contacts" and "administer CiviCRM").

!!! tip "Permissions for the Anonymous Role"

    Many sites want anonymous visitors to have access to certain CiviCRM functionality. These permissions are enabled during installation for the Anonymous role. You should review them and modify if needed based on your requirements:

    * **access all custom data** : If you plan on collecting "custom" data from visitors in standalone forms or as they make a contribution - enable this permission.
    * **access CiviMail subscribe/unsubscribe pages** : If you are planning on using CiviMail, enable this permission to allow anonymous users to subscribe and unsubscribe from mailing lists via the web.
    * **access uploaded files** : If you plan on allowing visitors to upload or view photos or other files - enable this permission.
    * **make online contributions** : If you plan on soliciting online contributions from visitors, enable this permission for the "anonymous" role.
    * **profile listings and forms** : If you plan on collecting name and address or other information from visitors, enable this permission for the "anonymous" role.
    * **view event info** and **register for events** : If you plan to use CiviEvent and want to allow un-authenticated visitors to view event information and register for events online - enable these permissions for the "anonymous" role.
    * **view event participants** : Enable this permission to allow anonymous users to access participant listing pages for events.

## Create CiviCRM Contacts for Existing Backdrop Users {:#contacts-users}

Once installed, CiviCRM keeps your Backdrop Users synchronized with corresponding CiviCRM contact records. The 'rule' is that there will be a matched contact record for each Backdrop user record. Conversely, only contacts who are authenticated users of your site will have corresponding Backdrop user records.

When CiviCRM is installed on top of an existing Backdrop site, a special CiviCRM Administrative feature allows you to automatically create CiviCRM contacts for all existing Backdrop users:

* Login to your Backdrop site with an administrator-level login
* Click the **CiviCRM** link in the main navigation block
* If your Backdrop site makes use of the `db_prefix` setting (in `settings.php`), in de top bar click **Administer » System Settings » CMS Database Integration** , and update the box for the Backdrop Users Table Name so that it includes the prefix.
* Click **Administer** in the menu bar
* Click **Users and Permissions** from the drop-down menu, then select **Synchronize Users to Contacts**

## Review the Configuration Checklist {:#checklist}

The **Configuration Checklist** provides a convenient way to work through the settings that need to be reviewed and configured for a new site. You can link to this checklist from the installation success page AND you can visit it at any time from **Administer** » **Administration Console** » **Configuration Checklist**.

## Test-drive CiviCRM {:#test-drive}

There should now be a **CiviCRM** link in your Backdrop menu. Click that link and the CiviCRM Menu, Shortcuts, Search and New Individual Blocks should appear. You can now explore CiviCRM end-user features and begin configuring CiviCRM for your site/organization needs.

## Trouble-shooting Resources {:#troubleshooting}

* Review the [Troubleshooting](/troubleshooting.md) page for help with problems you may encounter during the installation.
