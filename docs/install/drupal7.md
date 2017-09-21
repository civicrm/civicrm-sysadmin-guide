# Installing CiviCRM for Drupal 7

!!! note "Automated Installer for CiviCRM 4.7 on Drupal 7.x"

    This page provides instructions for using the web-based **Automated Installer**. This is the recommended installation method.

    If you are **upgrading CiviCRM, including from a prior version of v4.7 -** **[use these instructions](https://wiki.civicrm.org/confluence/display/CRMDOC/Upgrading+CiviCRM+for+Drupal+7)**.

    **Before beginning the install process, please verify that your server meets all the requirements for CiviCRM 4.7.**

    * **Drupal 7.x** : CiviCRM 4.7 is built to run under Drupal 7 (or [Drupal 6](https://wiki.civicrm.org/confluence/display/CRMDOC/Installing+CiviCRM+for+Drupal+6)).
    * **PHP:** See [CiviCRM PHP Requirements](https://wiki.civicrm.org/confluence/display/CRMDOC/CiviCRM+PHP+Requirements)
    * **PHP memory_limit** set to between 256 and 512 megabytes.
    * **MySQL 5.1.x or higher with INNODB support** : CiviCRM is compatible with the current [generally available MySQL release](http://dev.mysql.com/downloads/mysql). Trigger permission is required. SUPER privileges are required in MySQL 5.1 if binary logging is enabled. See the [CiviCRM MySQL Permission Requirements](https://wiki.civicrm.org/confluence/display/CRMDOC/CiviCRM+MySQL+Permission+Requirements) page.
     Configuration variable thread_stack >=192k.
    * **PCRE with Unicode properties support** ([more info](http://forum.civicrm.org/index.php/topic,9316.0.html "CentOS, CiviCRM and PCRE Unicode Properties")).
    * MAMP XCache in-compatibility* - Several people have reported "white screen of death" trying to run CiviCRM 4 with MAMP's XCache enabled (check MAMP > Preferences > PHP > Cache).
    * eAccelerator - Several people have reported problems with this performance optimiser ([http://eaccelerator.net](http://eaccelerator/)) - "white screen of death" and "snippet type is unrecognised".

**Table of Contents**

* 1. Drupal Installed

* Verify Drupal Database Settings

* 2. Tell Drupal where to find the CiviCRM 4.7 Module
* 3. If using CiviCRM in another language than English, install the localization files
* 4. Run the Installer
* 5. Review Permissions
* 6. Create CiviCRM Contacts for Existing Drupal Users
* 7. Review the Configuration Checklist
* 8. Test-drive CiviCRM
* Trouble-shooting Resources

## Drupal Installed

The automated installer requires Drupal 7.x.. If you do not have the required version of Drupal installed, refer to:

* [Drupal Installation Guide](http://drupal.org/getting-started)

### Verify Drupal Database Settings

You will need to know the database settings for your Drupal installation prior to running the CiviCRM installer:

You can look up these values in your Drupal settings.php file (located by default in your <drupal_root>/sites/default directory)

```
$db_url = 'mysql://dbuser:dbpassword@localhost/drupal';
```

or

```
$databases = array (
  'default' =>
  array (
    'default' =>
    array (
      'database' => 'drupal',
      'username' => 'dbuser',
      'password' => 'dbpassword',
      'host' => 'localhost',
      'port' => '',
      'driver' => 'mysql',
      'prefix' => '',
    ),
  ),
);
```

In the above example:

| Setting | Value |
| --- | --- |
| Database Server | localhost |
| Drupal Database Name | drupal |
| Database User Name | dbuser |
| Database User Password | dbpassword |

## Tell Drupal where to find the CiviCRM 4.7 Module

First, download CiviCRM and install the files like you would any other module:

* As administrator in Drupal, go to Modules, click on **Install New Module** , and it will ask you to fill in the URL of the module. It will then fetch it and install it for you. You will need to enable the **Update Manager** module in the drupal modules page to see the **Install New Module** link.
* Alternatively, you can upload and extract the file in <DRUPAL ROOT>/sites/all/modules

The result will be that the CiviCRM directory will be at <DRUPAL ROOT>/sites/all/modules/civicrm

The most up-to-date version of CiviCRM will always be available at [https://civicrm.org/download](https://civicrm.org/download) (though you may have to endure a fundraising pitch).

**Do NOT** proceed to "activate" the module - that will happen automatically in step 4.

## If using CiviCRM in another language than English, install the localization files

See the [internationalisation and localisation](https://wiki.civicrm.org/confluence/pages/viewpage.action?pageId=88408149) page about how to install files for running CiviCRM in languages other than American English.

## Run the Installer

The installer will verify that you've downloaded the correct version of CiviCRM, and will check your server environment to make sure it meets CiviCRM requirements. It will then create and populate a database for CiviCRM as well as create your CiviCRM settings file (civicrm.settings.php).

* Login to your Drupal site with Administrator level permissions.
* Point your web browser to the following URL:

**http://<your_drupal_home>/sites/all/modules/civicrm/install/index.php**

* You should see the **CiviCRM Installer** screen.
    * Initially, you will see a red bar with the message "These database details don't appear to be correct." This is expected as you haven't entered your database settings yet.
    * If you see other errors, check the **Requirements** details at the bottom of the page for more information. You will need to correct any issues before continuing.
* Fill in the CiviCRM Database Settings.

!!! tip "Where Should I Store CiviCRM Data?"
     CiviCRM may be configured to use your existing Drupal database, or a separate (new) database. Using a separate database is generally preferred - as it makes backups and upgrades easier. The installer will create a new database for CiviCRM automatically if you enter a database name that doesn't already exist on your database server AND the database user you enter has permission to create databases. In case the installer does not automatically create a new database, simply create a new one following the same process as creating a new database for Drupal. Note that if you plan to use the Drupal Views module to display CiviCRM data within your Drupal pages, and if you are going to use separate databases for Drupal and CiviCRM, you need to ensure that your Drupal database user has SELECT permissions for your CiviCRM database. |

    * Fill in the Drupal Database Settings for your existing Drupal database (as noted in step 2 above).

    !!! check "Loading Sample Data"
     The Installer includes an option to load a set of sample contact, group, and relationship data by default. Sample data can provide a useful head-start in learning to use CiviCRM. However, if you do NOT want the sample data to be loaded, just uncheck **Load sample data** under **Other Settings**. |

    * Select the appropriate language for the base installation. You will be able to add other languages after the installation for multi-lingual sites.
    * Click the **Check Requirements and Install CiviCRM** button.
        * The installer will configure your databases, create the settings file and redirect you to your Drupal Home page.
        * If you still see a red bar with the message "These database details don't appear to be correct." - check the Database Details section below your settings for specific errors and problems. Once you correct these problems, click "Recheck requirements" to verify your settings before continuing.
        * If you are on a Windows machine and get the message "The user account used by your web-server needs to be granted write access to the following directory in order to configure the CiviCRM settings file:
     C:<drupal path>/sites/default" even after changing directory permission in Explorer, see [http://forum.civicrm.org/index.php/topic,5056.msg23720.html#msg23720](http://forum.civicrm.org/index.php/topic,5056.msg23720.html#msg23720) for instructions how to change the permissions using CMD.
        * Once you see the green "You're ready to install!" message - you can click **Check Requirements and Install CiviCRM**

    ## 5. Review Permissions

    !!! check

    Note that Drupal tries to create the /files/ directory (and make it writeable), but only when saving admin/settings. Same holds for /temp directory, and a /uploads/ directory in the CiviCRM module root. On a brand-new Drupal install, this directory may be missing. Even on an existing installation, if file permissions are not set properly, the directory may be missing. If enabling the **civicrm** module generates errors regarding the files directory, you must create it (writeable) manually. Refer to Step 2 for instructions on directories to create and permissions to set.


* Go to **Administer » User management » Permissions**

* *- Verify that the Roles that you want to have access to CiviCRM have the appropriate permissions checked. CiviCRM is installed with a number of fixed permissions (such as "edit contacts" and "administer CiviCRM").

!!! tip "Permissions for the Anonymous Role"

    Many sites want anonymous visitors to have access to certain CiviCRM functionality. These permissions are enabled during installation for the Anonymous role. You should review them and modify if needed based on your requirements:

    * **access all custom data** : If you plan on collecting "custom" data from visitors in standalone forms or as they make a contribution - enable this permission.
    * **access CiviMail subscribe/unsubscribe pages** : If you are planning on using CiviMail, enable this permission to allow anonymous users to subscribe and unsubscribe from mailing lists via the web.
    * **access uploaded files** : If you plan on allowing visitors to upload or view photos or other files - enable this permission.
    * **make online contributions** : If you plan on soliciting online contributions from visitors, enable this permission for the "anonymous" role.
    * **profile listings and forms** : If you plan on collecting name and address or other information from visitors, enable this permission for the "anonymous" role.
    * **view event info** and **register for events** : If you plan to use CiviEvent and want to allow un-authenticated visitors to view event information and register for events online - enable these permissions for the "anonymous" role.
    * **view event participants** : Enable this permission to allow anonymous users to access participant listing pages for events.

## Create CiviCRM Contacts for Existing Drupal Users

Once installed, CiviCRM keeps your Drupal Users synchronized with corresponding CiviCRM contact records. The 'rule' is that there will be a matched contact record for each Drupal user record. Conversely, only contacts who are authenticated users of your site will have corresponding Drupal user records.

When CiviCRM is installed on top of an existing Drupal site, a special CiviCRM Administrative feature allows you to automatically create CiviCRM contacts for all existing Drupal users:

* Login to your Drupal site with an administrator-level login
* Click the **CiviCRM** link in the main navigation block
* If your Drupal site makes use of the db_prefix setting (in settings.php - cf. Step 2, above), in de top bar click **Administer » System Settings » CMS Database Integration** , and update the box for the Drupal Users Table Name so that it includes the prefix.
* Click **Administer** in the menu bar
* Click **Users and Permissions** from the drop-down menu, then select **Synchronize Users to Contacts**

## Review the Configuration Checklist

The **Configuration Checklist** provides a convenient way to work through the settings that need to be reviewed and configured for a new site. You can link to this checklist from the installation success page AND you can visit it at any time from **Administer** » **Administration Console** » **Configuration Checklist**.

## Test-drive CiviCRM

There should now be a **CiviCRM** link in your Drupal menu. Click that link and the CiviCRM Menu, Shortcuts, Search and New Individual Blocks should appear.

You can now explore CiviCRM end-user features and begin configuring CiviCRM for your site/organization needs. Refer to the [Administrator Guide](https://wiki.civicrm.org/confluence/display/CRMDOC/User+guide+supplement) for information on configuration tasks and options. Tips for creating CiviCRM Profiles (forms), custom data fields and programming custom data manipulation are included in the [**Drupal installation and configuration example**](https://wiki.civicrm.org/confluence/display/CRMDOC/Drupal+installation+and+configuration+example) document.

## Trouble-shooting Resources

Review the **[Installation and Configuration Troubleshooting](https://wiki.civicrm.org/confluence/display/CRMDOC/Installation+and+Configuration+Troubleshooting)** section of our wiki for help with problems you may encounter during the installation.

You can often find solutions to your issue by searching the **[installation support section of the community forum](http://forum.civicrm.org/index.php/board,6.0.html)** OR the [community mailing list archives](http://www.nabble.com/CiviCRM-Community-Mailing-List-Archives-f15986.html), and you can check out the **Installation section of our FAQs**.

If you don't find an answer to your problem in those places, the next step is to **[post a support request on the forum](http://forum.civicrm.org/index.php/board,6.0.html)**.

To check compatibility with other Drupal modules see: [Drupal modules incompatible with CiviCRM](https://wiki.civicrm.org/confluence/display/CRMDOC/Drupal+modules+incompatible+with+CiviCRM)