# Version-specific upgrade tasks

In general the steps to upgrade CiviCRM are identical no matter which version you're upgrading _from_ and _to_ &mdash; however this page offers some exceptions.

Here you will find special steps needed when your upgrade crosses certain CiviCRM versions.

For example, if you are upgrading from CiviCRM 4.1 to CiviCRM 4.3, then you should check this page for *both* "CiviCRM 4.2" and "CiviCRM 4.3" since your upgrade "crosses" both of those versions.

## CiviCRM 5.0

### Flush CiviCRM cache

After upgrading from CiviCRM 4.7.31, the cache must be flushed to resolve a mailing bug in the generated javascript. The upgrade web interface will take care of this automatically, but if you are running the database migration on the command-line, run `cv flush` to cleanup caches.

## CiviCRM 4.3

### Settings changes

**Background:** Settings are usually stored in the civicrm_setting table, where they're easily maintained. But there are occasions when developers might find it useful to override them. For instance, the development environment might mirror the production environment, except for a handful of settings. Adding a few lines to the civicrm.settings.php file can be much easier and less error-prone than changing production settings for development.

The settings in the `civicrm_setting` table are keyed on `domain`, `group`, and `name`. Here's an example using the CiviCRM 4.1 mechanism:

```
define('URL Preferences.customCSSURL', 'http://example.com/css');...
```

The `domain` is determined by the civicrm.settings.php file, the _group_ is 'URL Preferences', the _name_ is 'customCSSURL', and the `value` is `http://example.com/css`. If you're uncertain of the valid values these can take, inspect the `civicrm_setting` table.

**The change:** In CiviCRM 4.3 and later, the format has changed to:

```
global $civicrm_setting;
```

```
$civicrm_setting['URL Preferences']['customCSSURL'] = 'http://example.com/css';...
```

### Password must be supplied to cron

If you are running scheduled jobs using `CLI.php`, you will need to reconfigure cron to include a password. Scheduled jobs will no longer run if the password is not provided..


## CiviCRM 4.2

All CiviCRM-related cron jobs will stop working as soon as any site is upgraded from 4.1.x and below. If you are upgrading from a version prior to 4.2, cron jobs will need to be reconfigured using the new [Scheduled Jobs](setup/jobs.md) method.

