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
