# Override Paths

For a number of system paths CiviCRM will try to work out the correct path and url for the particular folder. However sometimes CiviCRM isn't able to properly calculate the paths correctly. CiviCRM system administrators are able to override these path variables within the CiviCRM settings file.

## Acceptable paths

The following are paths that can be overridden in civicrm.settings.php

* `civicrm.log` - Path to the directory where CiviCRM is to store it's log files - default: `[civicrm.private]/ConfigAndLog`
* `civicrm.private` - location of where private CiviCRM files such as the log files should be stored by default this is civicrm folder in the upload files directory of your CMS
* `civicrm.compile` - Location of the Smarty compile directory - default: `[civicrm.private]/templates_c`
* `civicrm.packages` - Location where CiviCRM 3rd party packages are installed - default `[civicrm.root]/packages`
* `civicrm.bower` - Location of where the 3rd party Javascript code is installed to - default: `[civicrm.root]/bower_components/`
* `civicrm.files` - Location of where to store CiviCRM upload files.
* `civicrm.root` - Location of the civicrm library code
* `civicrm.l10n` - Location of the CiviCRM l10n directory - default: `[civicrm.root]/l10n`

## Override paths in civicrm.settings.php

To override paths within your civicrm.civicrm.php you need to include code like. You can override one or both of the 2 path variables either the system path or the url to find the contents.

```php
global $civicrm_paths;
$civicrm_paths['civicrm.packages']['path'] = '/var/www/testing/vendor/civicrm/civicrm-packages/';
$civicrm_paths['civicrm.packages']['url'] = 'https://example.com/vendor/civicrm/civicrm-packages/';
```
