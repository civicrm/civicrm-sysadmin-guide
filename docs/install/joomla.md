# Installing CiviCRM for Joomla

## Scope of this guide and alternative installation methods

This guide covers standard installation of CiviCRM for production use. For installing a development environment, refer to the [section on Buildkit in the Developer Documentation](https://docs.civicrm.org/dev/en/latest/tools/buildkit/).

## Before installing

1. Ensure that your system meets the [requirements](/requirements.md).
1. Install Joomla by referring to the [Joomla Installation Guide](https://docs.joomla.org/J3.x:Installing_Joomla) if needed.

!!! note "Path for Joomla"
    The rest of these instructions assume that you have Joomla installed in `/var/www/JOOMLA_ROOT`. Adjust paths as needed.

## Download and Un-tar CiviCRM Code {:#download}

All CiviCRM code and packages used by CiviCRM (such as PEAR libraries) are included in the compressed CiviCRM distribution files ('tarballs'). Follow these steps to download and install the codebase:

* Download the appropriate tarball file from [civicrm.org/download](https://civicrm.org/download) within your browser. Tarball file-names include the CiviCRM version. For example, `civicrm-x.x.x-joomla.zip`. If you have command line access to your server, use `wget` and the URL of the file to pull a copy directly to your server. Then you can skip the next step.
* Upload this file to a folder in your root Joomla directory. Recommended location: `JOOMLA_ROOT/tmp/`.
* Unzip the package, which will create a directory called: `com_civicrm`. On cPanel, you can use the File Manager's Extract function to unzip the file you uploaded.

Note: when installing a new version over an old one, please check first "5. Trouble-shooting resources" below

## Run the Installer {:#installer}

* Login to your Joomla Administrator site.
* Go to Extensions >> Install/Uninstall.
* Use **Install from Directory** and enter the full path to the un-zipped com_civicrm directory, which should be something like: `JOOMLA_ROOT/tmp/com_civicrm` . In most Joomla installations, the root directory and /tmp/ folder will already populate the "install from directory" field. You must simply add the /com_civicrm/ subfolder at the end. Click Install. If that doesn't work:
    * put the full file system path to the `com_civicrm`, something like `/home/yourusername/public_html/yourjoomladirname/tmp/com_civicrm/` - that worked for me on both a cPanel and a Virtualmin server.
* You should see "Component successfully installed" message.

!!! warning

    If you get the following error when installing or upgrading CiviCRM for Joomla - you can downloading the alternate Joomla install file - `civicrm-x.x.x-joomla-alt.zip` - which doesn't require built-in unzip functionality on your server. Then repeat the install steps starting with [running the installer](#installer).

    > Your PHP version is missing zip functionality. Please ask your system administrator / hosting provider to recompile PHP with zip support.

## Create CiviCRM Contacts for Existing Joomla Users {:#contacts-users}

Once installed, CiviCRM keeps your Joomla Users synchronized with corresponding CiviCRM contact records. The 'rule' is that there will be a matched contact record for each Joomla user record. Conversely, only contacts who are authenticated users of your site will have corresponding Joomla user records.

When CiviCRM is installed on top of an existing Joomla site, a special CiviCRM Administrative feature allows you to automatically create CiviCRM contacts for all existing Joomla users:

* Login to your Joomla site with an administrator-level login
* Click the **Components > CiviCRM > CiviCRM Home** link in the main navigation block
* Click **Administer > Users and Permissions > Synchronize Users-to-Contacts**



## Troubleshooting Resources {:#troubleshooting}

* If the CiviCRM component does not install correctly (for example you get a blank screen instead of the confirmation page), delete the `~/components/com_civicrm` and `~/administrator/components/com_civicrm` and `~/media/civicrm directories` manually and then try each of the following before attempting to reinstall:

    * In your `php.ini` file or in `.htaccess` file in the Joomla root folder (if your server allows it), increase the `max_execution_time` to `600` and memory limit to more than 64M. Add the following to the `.htaccess` file in your Joomla root folder
    
        ```
        php_value memory_limit 128M
        php_value register_globals off
        php_value max_execution_time 600
        ```
        
        Or if you can't change the `.htaccess` file you can add a php.ini (or `.user.ini`) file in Joomla root folder or all directories, depending on your web server or hosting company.
        
        ```
        memory_limit = 128M
        register_globals = off
        max_execution_time = 600
        ```
    
    * CiviCRM is packaged with all the libraries (PEAR etc) that it uses. However a misconfigured or overly restrictive `open_basedir` directive on your web server might interfere with CiviCRM's ability to access some required files or directories. To turn `open_basedir` off, add this to your `vhost.conf` file: `php_admin_value open_basedir none` or ask your host to either turn it off or ensure that it is not preventing access to required directories (e.g. if you move configuration files or temp folders outside your web root). The configuration of sub domains might cause related issues: try installing in the main domain root or a sub folder instead.

* If CiviCRM screens are not displaying properly and/or javascript widgets are not functioning, check your CiviCRM Resource URL (Administer >> System Settings >> Resource URLs). For Joomla installs, it should be something like: `http://example.org/administrator/components/com_civicrm/civicrm`

* Review the [Troubleshooting](/troubleshooting.md) page for help with problems you may encounter during the installation.

