# Extensions

!!! tip "See Comparison of Add-On Formats"
    There are many ways to extend CiviCRM with benefits and drawbacks for each. See [Packaging Formats](https://docs.civicrm.org/dev/en/latest/extensions/packaging/) to help decide which is appropriate for your current needs. This page covers "native" CiviCRM extensions - which are NOT restricted to use with a specific CMS. CiviCRM can also be "extended and customized" using CMS-specific extensions - e.g Drupal modules, Joomla components, and WordPress plugins. Refer to the [Packaging Formats](https://docs.civicrm.org/dev/en/latest/extensions/packaging/) page and to each CMS's documentation for more information.

!!! note "More information about CiviCRM Extensions"
    To find out more about developing CiviCRM extensions, see the [Extensions](https://docs.civicrm.org/dev/en/latest/extensions/) page in the Developer Guide.

## Enabling extensions

CiviCRM extensions allow you to install additional features for your site. They can provide new functionality in many areas including: Custom Searches, Report Templates and Payment Processors. CiviCRM "native" extensions which have been shared using the [Extensions Directory on CiviCRM.org](http://civicrm.org/extensions) and which have a stable release compatible with your version of CiviCRM can be installed automatically from Administer >> System Settings >> Manage Extensions.

Configure the extensions directory:
 Before installing extensions, you need to configure a directory where your extensions will be stored.

* Go to: "Administer » System Settings » Directories"
* fill in "CiviCRM Extensions Directory" field and click "Save" at the bottom of the page.

!!! tip

    It's important to put your extensions directory outside of the $civicrm_home directory (the one where your codebase is installed) in order to avoid problems during upgrade. The extensions directory must be readable (so it can execute the extension code) and write-able (so it can download initial and updated versions of extensions) by your web server.


### Suggested directory location

#### Drupal

Remembering that mysite == default for most single site installs:

<docroot>/sites/mysite/files/civicrm/ext

#### Joomla

`<docroot>/media/civicrm/ext`

#### WordPress

`<docroot>/wp-content/uploads/civicrm/ext`


Configure the extensions resource url:
 This is the base url through which resources (css, js) of your extensions are accessible to the web.

* Go to "Administer » System Settings » Resource URLs".
* Fill in "<label for="extensionsURL">Extension Resource URL</label>," and click "Save".
 Note that this URL corresponds to the same location as the "Extensions Directory" above.

TODO: It should be explained what in the extensions directory should be made web-accessible, if the directory itself is outside of web root.

See the list of extensions:
 Finally, you should be able to

* Go to "Administer » System Settings » Manage CiviCRM Extensions" and
* See the list of available extensions.

This list is automatically downloaded from available and compatible native extensions on the CiviCRM server. To find out more about a specific extension, click the arrow on the left side of the extension name. This will reveal more details about this extension including the author, release version, release date and licensing.

!!! note "Other Extension Locations"

    CiviCRM will find, use and (via the web interface) manage extensions in the url and path location defined above, the "default" extension location for your install.

    But CiviCRM will also find and use extensions in other locations. These locations might be populated by other mechanisms - e.g. a vetted subset of all extensions that are made available by default by a hosting provider. Specifically, CiviCRM will scan three directories and their subdirectories for info.xml files, similar to the mechanism that Drupal uses to automatically detect modules (in fact, using almost identical code). The three directories correspond to three extension 'containers', as defined in the function "getFullContainer" of the file "CRM/Extension/System.php"

    1. default : the one identified the settings. But note that although CiviCRM will only manage the extensions in the immediate directory location, it will still find them in subdirectories.
    1. civiroot : the civicrm module code root, i.e. where the civicrm-version.php file is. This allows CiviCRM to be distributed with extensions, and different 'distributions' of CiviCRM to be created.
    1. cmsvendor : a subdirectory 'vendor' of the CMS root (typically, the docroot of the web service). Analagous to the (core) module directory of the CMS. This one is not available for WordPress, only Drupal and Joomla, as noted in the code.

!!! note "Some extensions listed on CIviCRM.org are NOT listed in Manage Extensions on your site"

    The Extensions Directory on CiviCRM.org includes several different types of extensions (including Drupal, Joomla and WordPress specific modules). The Manage Extensions function on your site lists and provides automatic installation only for extensions that:

    * Are "native CiviCRM" extensions (not CMS specific)
    * Have been "approved for automated distribution" by one of our moderators
    * Have a "stable" release which is compatible with your version of CiviCRM

    If you wish to install "unstable" releases (alpha/beta/pre-release/etc) through the "Manage Extensions" screens, then you can change the _extension repository URL_. This is the URL used by CiviCRM to fetch the list of available extensions, and it can be customized by editing "civicrm.settings.php":

    **civicrm.settings.php - Extension Repo URL**

    ```
    <?php
    ...
    global $civicrm_setting;
    // NB: for both Option A and Option B, you can use 4.5 or 4.6 etc as the {ver}
    // Option A: Default repository URL; civicrm.org will make its best recommendation for stable/compatible extensions
    $civicrm_setting['Extension Preferences']['ext_repo_url'] = 'http://civicrm.org/extdir/ver={ver}|cms={uf}';
    // Option B: Customized repository URL which filters on "any release status" (stable, alpha, beta, etc)
    $civicrm_setting['Extension Preferences']['ext_repo_url'] = 'http://civicrm.org/extdir/ver={ver}|cms={uf}|status=';
    ...
    ```


## Installing a new extension

In order to install a listed extension all you need to do is to click "Install" link next to given extension. The extensions package will be downloaded and set up for use in your system. Extensions may update your database and affect how your CiviCRM site functions. Best practice is to install any new extension on a test copy of your site first and verify that it functions as expected.

!!! tip "Manual installation of native extensions"

    If you want to install a native CiviCRM extension which is not listed under Manage Extensions automatically:

    * Download the extension package from the download URL provided by the developer
    * Unzip / untar the package and place it in your configured extensions directory
    * If the package has been constructed properly - when you reload the Manage Extensions page the new extension should be listed with an Install link.

## Disable automatic installations of extension

For security and policy reasons, you might want to disable the option to download extensions and only rely on the manual installation described above. It has the benefit of also suppressing a couple of possible warning messages: a message if you want to let this folder read only and one if your server does not have the PHP-ZIP extension - "You will not be able to install extensions at this time because your installation of PHP does not support ZIP archives".

To do so, you need to add this following line on your civicrm.settings.php

```
global $civicrm_setting;$civicrm_setting['Extension Preferences']['ext_repo_url'] = false;
```

As a result of disabling the automatic installations of extensions, on the New Extensions page [http://example.org/civicrm/admin/extensions?reset=1](http://example.org/civicrm/admin/extensions?reset=1) (Drupal), you will see a message "The system administrator has disabled this feature."

## Enabling and disabling extensions

If you installed an extension, but don't want functionality provided by it to be available in the system, you can click the "Disable" link next to the installed extension.

## Upgrading extensions

If new version of an extension installed in your system becomes available, you will see an "Upgrade" link next to it – clicking it will upgrade the extension's source code. If there are also database changes, then a new alert will prompt you to apply them as well.

!!! note "Upgrade Details"

    The mechanics of the upgrade process depend on a few rules:

    * If the extension is a 3.x-style specialized extension (i.e. "Payment Processor Extension", "Custom Search Extension", or "Report Extension"), then both the _source code_ and the _metadata_ will be uninstalled and reinstalled. (3.x-style extensions should not have other data.)
    * If the extension is a 4.2+-style module extension, then the _source code_ will be uninstalled and reinstalled, but the _metadata_ will be modified in-place, and (if applicable and if approved by the admin) the database will be upgraded using [hook_civicrm_upgrade](https://docs.civicrm.org/dev/en/latest/hooks/hook_civicrm_upgrade/) (or, more specifically, using an [Upgrader](https://docs.civicrm.org/dev/en/latest/extensions/civix/#generate-upgrader).

## Uninstalling extensions

In order to uninstall extension, you need to disable it first - once disabled, you will see the "Uninstall" link. Click it in order to completely remove the extension from your system, including permanent deletion all of its custom data. To preserve the data, take a backup before uninstalling.
