This section is intended to describe some tips & tricks to Optimize the CiviCRM Setup

## Smarty

### Disable Compile Check

By default CiviCRM is set to TRUE the Smarty option `compile_check`, which forces to recompile Smarty templates on every call.
Recompiling does not need to happen unless a template or config file is changed. Typically you enable this during development, and disable for production.
To disable this in CiviCRM, edit this constant in `civicrm.settings.php` :

```php
define('CIVICRM_TEMPLATE_COMPILE_CHECK', FALSE);
```
