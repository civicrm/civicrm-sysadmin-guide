# Installing CiviCRM for Joomla

!!! note "Install CiviCRM 4.7 on Joomla"

    **IMPORTANT: 4.7 requires Joomla 2.5.x or 3.x.x**
     **Joomla 2.5 has reached _End of Life_ (EOL) and is no longer supported by the Joomla! Project.
     It is strongly recommended by the J! Project that 2.5 users upgrade to the most current version of 3.x .**

    **Before beginning the install process, please verify that your server meets the requirements for CiviCRM 4.7.**

    * **Joomla 2.5.x or 3.x.x** : CiviCRM 4.7 is built to run under Joomla 2.5x/3.x.x and **is not compatible with Joomla 1.0.x and Joomla 1.5.x sites**
    * **PHP:** See [CiviCRM PHP Requirements](https://wiki.civicrm.org/confluence/display/CRMDOC/CiviCRM+PHP+Requirements)
    * **PHP memory_limit** set to between 256 and 512 megabytes.
    * **MySQL 5.1.x or higher with INNODB support** : CiviCRM is compatible the current [generally available MySQL release](http://dev.mysql.com/downloads/mysql). Trigger permission is required. SUPER privileges are required in MySQL 5.1 if binary logging is enabled. See the [CiviCRM MySQL Permission Requirements](https://wiki.civicrm.org/confluence/display/CRMDOC/CiviCRM+MySQL+Permission+Requirements) page.
     Configuration variable thread_stack >= 192k.
    * **PCRE with Unicode properties support** ([more info](http://forum.civicrm.org/index.php/topic,9316.0.html)).

    If you are **upgrading from v3.3, v3.4, v4.1, v4.2 , v4.3, v4.5 or an earlier version of 4.6 -** **[use these instructions](https://wiki.civicrm.org/confluence/display/CRMDOC45/Upgrade+Joomla+Sites+to+4.6)**.

!!! danger

    CiviCRM is a resource heavy module and does not work well on GoDaddy or Site5 shared hosting. The install process typically does not complete successfully. The forums have a lot of posts on this topic.


## Joomla Installed

The automated installer requires Joomla 2.5.x or 3.x.x. If you do not have the required version of Joomla installed, refer to:

* [Joomla Installation Guide](https://docs.joomla.org/J3.x:Installing_Joomla)

!!! note "Path for Joomla"
 The rest of these instructions assume that you have Joomla installed in _/var/www/JOOMLA_ROOT_. Adjust paths as needed. |

## Download and Un-tar CiviCRM Code

All CiviCRM code and packages used by CiviCRM (such as PEAR libraries) are included in the compressed CiviCRM distribution files ('tarballs'). Follow these steps to download and install the codebase:

* Download the appropriate tarball file from [here](https://civicrm.org/download)[with your browser. Tarball file-names include the CiviCRM version. For example, **civicrm-4.7.x-joomla.zip**. If you have command line access to your server, use wget and the URL of the file to pull a copy directly to your server. Then you can skip the next step.](http://sourceforge.net/project/showfiles.php?group_id=177914)
* Upload this file to a folder in your root Joomla! directory. Recommended location: JOOMLA_ROOT/tmp/.
* Unzip the package, which will create a directory called: com_civicrm. On cPanel, you can use the File Manager's Extract function to unzip the file you uploaded.

Note: when installing a new version over an old one, please check first "5. Trouble-shooting resources" below

## Run the Installer

* Login to your Joomla Administrator site.
* Go to Extensions >> Install/Uninstall.
* Use **Install from Directory** and enter the full path to the un-zipped com_civicrm directory, which should be something like: **JOOMLA_ROOT/tmp/com_civicrm** /. In most Joomla! installations, the root directory and /tmp/ folder will already populate the "install from directory" field. You must simply add the /com_civicrm/ subfolder at the end. Click Install. If that doesn't work:
    * put the full file system path to the com_civicrm, something like /home/yourusername/public_html/yourjoomladirname/tmp/com_civicrm/ - that worked for me on both a cPanel and a Virtualmin server.
* You should see "Component successfully installed" message.

!!! warning

If you get the following error when installing or upgrading CiviCRM for Joomla! - you can downloading the alternate Joomla install file - **civicrm-4.7.x-joomla-alt.zip** - which doesn't require built-in unzip functionality on your server. Then repeat the install steps starting with step 3 above.
**Your PHP version is missing zip functionality. Please ask your system administrator / hosting provider to recompile PHP with zip support.** |

## Create CiviCRM Contacts for Existing Joomla Users

Once installed, CiviCRM keeps your Joomla Users synchronized with corresponding CiviCRM contact records. The 'rule' is that there will be a matched contact record for each Joomla user record. Conversely, only contacts who are authenticated users of your site will have corresponding Joomla user records.

When CiviCRM is installed on top of an existing Joomla site, a special CiviCRM Administrative feature allows you to automatically create CiviCRM contacts for all existing Joomla users:

* Login to your Joomla site with an administrator-level login
* Click the Components >> **CiviCRM** >>CiviCRM Home link in the main navigation block
* Click **Administer >> Users and Permissions >> Synchronize Users-to-Contacts**



## Trouble-shooting Resources

If the CiviCRM component does not install correctly (for example you get a blank screen instead of the confirmation page), delete the ~/components/com_civicrm and ~/administrator/components/com_civicrm and ~/media/civicrm directories manually and then try each of the following before attempting to reinstall:

* In your php.ini file or in .htaccess file in the Joomla root folder (if your server allows it), increase the max_execution_time to 600 and memory limit to more than 64M. **Add the following to the .htaccess file in your Joomla root folder**
```
php_value memory_limit 128M
php_value register_globals off
php_value max_execution_time 600
```

**Or if you can't change the .htaccess file you can add a php.ini (or .user.ini) file in Joomla root folder or all directories, depending on your web server or hosting company.**

```
memory_limit = 128M
register_globals = off
max_execution_time = 600
```

* CiviCRM is packaged with all the libraries (PEAR etc) that it uses. However a misconfigured or overly restrictive open_basedir directive on your web server might interfere with CiviCRM's ability to access some required files or directories. To turn open_basedir off, add this to your vhost.conf file: **php_admin_value open_basedir none** or ask your host to either turn it off or ensure that it is not preventing access to required directories (e.g. if you move configuration files or temp folders outside your web root). The configuration of sub domains might cause related issues: try installing in the main domain root or a sub folder instead.

If CiviCRM screens are not displaying properly and / or javascript widgets are not functioning, check your CiviCRM Resource URL (Administer >> System Settings >> Resource URLs). For Joomla! installs, it should be something like: http://(domainname)/administrator/components/com_civicrm/civicrm

Review the **[Installation and Configuration Troubleshooting](https://wiki.civicrm.org/confluence/display/CRMDOC/Installation+and+Configuration+Troubleshooting)** section of our wiki for help with problems you may encounter during the installation.

You can often find solutions to your issue by searching the **[installation support section of the community forum](http://forum.civicrm.org/index.php/board,2.0.html)** OR the [community mailing list archives](http://www.nabble.com/CiviCRM-Community-Mailing-List-Archives-f15986.html), and you can check out the **Installation section of our FAQs**.

If you don't find an answer to your problem in those places, the next step is to [post a support request on the forum](http://forum.civicrm.org/index.php/board,2.0.html).