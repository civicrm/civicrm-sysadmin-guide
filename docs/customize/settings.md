# Override CiviCRM Settings

CiviCRM provides a web-based interface for configuring its settings. This approach is easy-to-use for new administrators who maintain a single site, but it presents challenges for sophisticated administrators who manage many copies of CiviCRM. CiviCRM 4.1 introduced a new settings system which allows administrators to override some settings by managing a custom PHP file.

Further information about settings is on [Settings Reference](https://docs.civicrm.org/dev/en/latest/framework/setting/)

## Use Copied Databases by Overriding Server Directory and URL Settings

Some organizations run separate copies of CiviCRM for "production" ("live"), "testing", and/or "development." For example, if a developer wants to test a new feature, he might copy the database from the production system to the development system where he can safely test without interrupting normal users. Most of the settings should be copied exactly from production to development, but some server-specific settings – like the directory and URL settings – will always be different in production and development.

These server-specific settings are _usually_ determined automatically during installation – but if you copy the database, then the administrator must fix them to match the new system. Two administrative forms are provided to allow administrators to manually edit these settings:

Administer > System Settings > Directories
Administer > System Settings > Resource URLs

## Hidden Settings

There are also some settings which can not be set from the administration user interface - but which can be overridden using this same technique. These include the Community Messages URL, and whether or not to allow users to download extensions (refer to [Disable automatic installation of extensions](/customize/extensions.md) for more details).

## Override settings in civicrm.settings.php

If you regularly copy a production database to a test or development server, manually editing these settings may become cumbersome and error-prone. A faster alternative is to override the server-specific settings (e.g. directory and URL paths) by adding them to a local copy of the CiviCRM settings file (_civicrm.settings.php_).  First ensure this line exists in civicrm.settings.php before any override settings.
```
global $civicrm_setting;
```

## Override Temporary Files directory

To override the Temporary Files directory for a test site, add these lines to _civicrm.settings.php_:

```
// Override the Temporary Files directory
$civicrm_setting['Directory Preferences']['customFileUploadDir'] = '/var/www/testing/sites/default/files/civicrm/upload';
```

## List of Additional Settings

The table below shows some of the settings file "constants" which will override the database value if present in _civicrm.settings.php_ and administrative settings field.

| Admin Field (Form) | Settings File Override Example |
| --- | --- |
| **Temporary Files** (System Settings > Directories) | $civicrm_setting['Directory Preferences']['uploadDir'] = '/path/to/upload-dir' ; |
| **Images** (System Settings > Directories) | $civicrm_setting['Directory Preferences']['imageUploadDir'] = '/path/to/image-upload-dir' ; |
| **Custom Files** (System Settings > Directories) | $civicrm_setting['Directory Preferences']['customFileUploadDir'] = '/path/to/file-upload-dir'; |
| **Custom Templates** (System Settings > Directories) | $civicrm_setting['Directory Preferences']['customTemplateDir'] = '/path/to/template-dir'; |
| **Custom PHP Path Directory** (System Settings > Directories) | $civicrm_setting['Directory Preferences']['customPHPPathDir'] = '/path/to/custom-php-dir'; |
| **Extensions Directory** (System Settings > Directories) | $civicrm_setting['Directory Preferences']['extensionsDir'] = '/path/to/extensions-dir'; |
| **CiviCRM Resource URL** (System Settings > Resource URLs) | $civicrm_setting['URL Preferences']['userFrameworkResourceURL'] = 'http://example.com/example-resource-url/'; |
| **Image Upload URL** (System Settings > Resource URLs) | $civicrm_setting['URL Preferences']['imageUploadURL'] = 'http://example.com/example-image-upload-url'; |
| **Custom CiviCRM CSS URL** (System Settings > Resource URLs) | $civicrm_setting['URL Preferences']['customCSSURL'] = 'http://example.com/example-css-url' ; |
| **Extensions Resource URL** (System Settings > Resource URLs) | $civicrm_setting['URL Preferences']['extensionsURL'] = '[http://example.com/](http://example.com/)pathtoextensiondir' |
| **Disable display of Community Messages on home dashboard** | $civicrm_setting['CiviCRM Preferences']['communityMessagesUrl'] = false; |
| **Disable automatic download / installation of extensions** | $civicrm_setting['Extension Preferences']['ext_repo_url'] = false; |
| **Set How long HTTP Requests could last for before timing out** | $civicrm_setting['CiviCRM Preferences']['http_timeout'] = '0.5'; |
| **Enable SSL** | $civicrm_setting['CiviCRM Preferences']['enableSSL'] = false; |
| **Verify SSL** | $civicrm_setting['CiviCRM Preferences']['verifySSL'] = false; |

For a full list of settings, see [https://github.com/civicrm/civicrm-core/tree/master/settings](https://github.com/civicrm/civicrm-core/tree/master/settings)

!!! warning "Incorrect Paths May Cause Unexpected Behaviors"

    Review the path values you are saving to these override constants carefully before saving the file. Incorrect path definitions may cause display issues and / or other unexpected behavior in CiviCRM pages and forms. If you've browsed to your site when an incorrect Resource URL or Custom CSS value has been set, you'll probably need to do a forced refresh with your browser. You may also need to empty the browser cache to make sure the correct CSS and Javascript files get loaded.

!!! notice "HTTP Timeout Setting"

    From CiviCRM 5.15 onwards there is a hidden setting for the HTTP request timeout in seconds. The default for this is 5 seconds. Adjusting the timeout will affect both the page load times (lower time, pages will load faster), too low a timeout and CiviCRM status checks may not complete in time resulting in users seeing an incorrect status alert check failed warning message.
