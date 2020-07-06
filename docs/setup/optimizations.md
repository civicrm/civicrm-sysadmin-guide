This section is intended to describe some tips & tricks to Optimize the CiviCRM Setup

## Smarty

### Disable Compile Check

By default CiviCRM is set to TRUE the Smarty option `compile_check`, which forces to recompile Smarty templates on every call.
Recompiling does not need to happen unless a template or config file is changed. Typically you enable this during development, and disable for production.
To disable this in CiviCRM, edit this constant in `civicrm.settings.php` :

```php
define('CIVICRM_TEMPLATE_COMPILE_CHECK', FALSE);
```

## Smart Group Performance

Depending on the number of smart groups you have on your system this may help with the performance of your instance. It is recommended that you set the `smart_group_cache_refresh_mode` to be deterministic rather than opportunistic. The latter will cause Smart Groups to be rebuilt during an HTTP request on certain areas of the system which may slow down user experience. Deterministic cache refreshing will mean that you will need to set up a [scheduled job](../setup/jobs.md) to trigger the job `job.group_cache_flush` to trigger the smart group cache clearing. It is also recommended that the smart group cache timeout be set to something around 5-10 minutes.

## Exclude dirs that do not need to be scanned

This was introduced in 5.29.

CiviCRM has a utility called `CRM_Utils_File::findFiles()` which is used to locate various glob patterns of file, such as `*.info` files to find extensions and `*.xml` files etc. By default this will search every directory under the docroot, except those that begin with a dot, e.g. `.git`. However, there are a number of directories that may be large but will never contain things CiviCRM is looking for, so you can improve the efficiency of this process by excluding them.

You do this by providing a regex in a constant called `CIVICRM_EXCLUDE_DIRS_PATTERN` in your `civicrm.settings.php` file. The regex will be used to match on file system *absolute* paths, e.g. `/var/www/example.org/sites/default/files/civicrm/ext`.

For example, you might consider (if it makes sense in your environment) the following, which would exclude:

- All dirs starting with a dot (`.git`, `.svn`, `.secret`, `.env` etc.)
- All `node_modules` dirs - these can be enormous.
- All `js` and `css` dirs - if you're sure CiviCRM doesn't need to look in there (it doesn't as of 5.29)
- All `bower_components` and `packages` and `vendor` dirs - these contain 3rd party library code.
- The `sites/default/files/private` dir, which is a local example that might be where Drupal's private managed files are kept.

```php
<?php
if (!defined('CIVICRM_EXCLUDE_DIRS_PATTERN')) {
   define('CIVICRM_EXCLUDE_DIRS_PATTERN', '@/(\.|node_modules|js/|css/|bower_components|packages/|vendor/|sites/default/files/private)@');
}
```

Depending on a lot of local factors, this can speed up cache clears, extension installs etc. significantly (e.g. 10× faster has been measured).
