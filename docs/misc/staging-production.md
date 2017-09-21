# Moving CiviCRM instance from production to staging

Many websites have production and staging/development/test instances. When copying a CiviCRM instance from production to non-production, a number of changes should be made to prevent undesirable effects from having two or more copies of the site running. The clearest example is that each instance will attempt to send out emails for scheduled reminders and scheduled and in-process bulk mailings from CiviMail. Other possibilities include posting recurring transactions to certain payment processors for recurring contributions, sending text messages, and custom synchronizations with external systems.

The following explains support that has been added to better support disabling production activities like these when moving a CiviCRM instance from production to non-production.

1. To change a site instance from production to non-production mode (for example, when migrating a site from production to a staging or test or development instance), navigate to debugging page via Administer > System Settings > Debugging and Error Handling.

1. By default, 'Environment' is Production. Other options values can be defined, with 'Staging' and 'Development' defined by default and on upgrade.

1. When Environment is set to anything other than Production and saved, a status message is displayed indicating that Outbound Email has been turned off and Scheduled Jobs will not be executed.

1. The outbound email will be set to “Disable Outbound Email”. Logging of mails will not be done until user explicitly sets the value in civicrm.settings.php & changes the outbound setting to mail().

1. On the Scheduled jobs page, a bolded message in red is displayed indicating that execution of scheduled jobs are terminated even though they appear to be running (appropriate text to be supplied later).

1. There will also be a link to this wiki page containing further documentation.

1. If one wishes to run a specific scheduled job when the Environment <> Production, add a param:

runInEnvironment={Production,Staging}

to the individual scheduled job. The values in the comma delimited list need to match the option values for the Environment option group or they will be ignored. When the runInEnvironment parameter is present, if the current environment is not present in the list, then the job does not run. Direction to do this is also supplied in help text message on the Scheduled Jobs page in CiviCRM.

1. Display a warning on dashboard if non-prod mode is enabled, similar to debug mode enabled: 'The environment of this CiviCRM instance is set to xxx. Certain functionality like scheduled jobs has been disabled.'