# Moving CiviCRM instance from production to staging

Many websites have production and staging/development/test instances. When copying a CiviCRM instance from production to non-production, a number of changes should be made to prevent undesirable effects from having two or more copies of the site running. The clearest example is that each instance will attempt to send out emails for scheduled reminders and scheduled and in-process bulk mailings from CiviMail. Other possibilities include posting recurring transactions to certain payment processors for recurring contributions, sending text messages, and custom synchronizations with external systems.

The following explains support that has been added in CiviCRM 4.7.25+ to better support disabling production activities when moving a CiviCRM instance from production to non-production.

## Changing your Environment

1. By default, 'Environment' is Production. Other options values can be defined, with 'Staging' and 'Development' defined by default.

1. To change a site instance from production to non-production mode (for example, when migrating a site from production to a staging or test or development instance), navigate to debugging page via **Administer > System Settings > Debugging and Error Handling**.

1. You can also set the Environment in `civicrm.settings.php`.  Newer installs of CiviCRM have `global $civicrm_setting` at the top of the file and `$civicrm_setting['domain']['environment'] = 'Production';` commented out.  If you have upgraded from an older version of CiviCRM, you can add these manually to `civicrm.settings.php`.

## Effects of changing your Environment

1. The outbound email will be set to “Disable Outbound Email”. Logging of mails will not be done until user explicitly sets the value in `civicrm.settings.php` and changes the outbound setting to `mail()`.
     1. This does NOT happen if you have set your Environment via `civicrm.settings.php`.

1. If one wishes to run a specific scheduled job when the in a non-production Environment, add `runInNonProductionEnvironment=TRUE` to that job's parameters.

1. Additional changes may be added by extensions that are Environment-aware - for instance, disabling recurring payments.
