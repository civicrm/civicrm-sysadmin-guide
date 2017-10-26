# Scheduled jobs

CiviCRM relies on a number of scheduled jobs that are run on a regular
basis to keep data up to date and to perform certain tasks. These jobs
must be triggered by a [cron](http://en.wikipedia.org/wiki/Cron) that
runs regularly on your web hosting server.

Examples of scheduled jobs include:

-   the membership processing job, which updates membership statuses
-   the event waiting list job, which invites people on the waiting list
    to register for events when spaces become available
-   the CiviMail send and process script which handle the sending and
    processing of emails
-   the CiviReport scripts which handle the emailing of reports on a
    regular basis.

Some jobs need to be run very frequently, for example the email send
task tends to be run once every 5 or 10 minutes. Others need to be run
less frequency, for example the membership update script may be run just
once a day.

There are two ways of configuring scheduled jobs:

-   via the user interface, which you can configure at **Administer >
    System Settings > Scheduled Jobs** and a single consolidated cron
    job
-   by configuring multiple cron jobs for specific tasks

These methods are discussed below.

## What is cron?

Cron (think "**cron**ology" or "**cron**ograph") is a time-based
automatic scheduler that triggers certain programs to run on your web
server. Generally speaking, cron (also known as 'a cronjob') can be
setup in your web hosting control panel or configured by the server
administrator in charge of your web hosting. We recommend choosing web
hosting that offers cron as a free service.

Before scheduled jobs configured via the user interface will actually
work you will need to configure a web server CRON job. There are CRON
configuration examples found in the [Running Command-line Scripts via
URL](http://wiki.civicrm.org/confluence/display/CRMDOC/Running+Command-line+Scripts+via+URL) wiki
page.

## Configuring Scheduled Jobs via the user interface

The user interface for scheduled tasks is designed to make it easy for
people to set up scheduled jobs, and avoid having to create or edit a
cron job each time you want to make a change to a scheduled job. Most
smaller installations should be fine configuring and running scheduled
jobs via the user interface. Larger sites should consider creating cron
jobs in the normal way for each task. That requires some system
administration skills.

The Scheduled Jobs page (**Administer**> **System Settings** >
**Scheduled Jobs**) is designed to make it easy to set up scheduled jobs
and to monitor when they were last run. It shows a list of all available scheduled
jobs. You can edit each one and set its frequency (hourly, daily, weekly, monthly, quarterly, yearly or every time cron is run which is typically every 5-10 minutes), any relevant parameters, and the earliest date/time for the first/next execution of the job.

You can find an up-to-date list of all scheduled jobs and the parameters
that can be sent to them on the [Managing Scheduled
Jobs](http://wiki.civicrm.org/confluence/display/CRMDOC/Managing+Scheduled+Jobs)
wiki page.

Some jobs perform special data update tasks and are not designed to be
run automatically or repeatedly. These are: "Update Greetings and
Addressees" and "Set Renewal Reminder Dates". Details about when to run
them are provided on the [Managing Scheduled
Jobs](http://wiki.civicrm.org/confluence/display/CRMDOC/Managing+Scheduled+Jobs)
wiki page.

## Manual execution of scheduled jobs

The scheduled jobs page can also be used to run scheduled jobs on a one-off basis. This is useful for some of the scheduled jobs that are
designed to be run on a less regular basis, including the geo-coding job
and the greetings and addressees job. Execute a job manually by
clicking the **More > Execute Now** link for the given job
at **Administer > System Settings > Scheduled Jobs**.

## Scheduling specific jobs via individual cron tasks

System administrators more commonly talk about scheduled jobs as cron
jobs. If you run a lot of scheduled jobs on large data sets you may wish
to consider getting an experienced system administrator to set these up
using cron.  This will allow more granular control over when and how
these jobs are executed, which may be useful if these processes put
significant load on your server.

Note scheduled jobs that were run via an individual cron job directly do
not automatically appear here. They need to be configured to do so.
Details on triggering specific jobs via command-line, URL, Drush and
other methods can be found in the [Managing Scheduled
Jobs](http://wiki.civicrm.org/confluence/display/CRMDOC/Managing+Scheduled+Jobs)
wiki page.


## Managing Scheduled Jobs


### Overview

CiviCRM includes a number of "Jobs" which process data and handle batch tasks "in the background". The available jobs are listed below, with a description of their purpose and required parameters. Your site / system administrator should review the available jobs, and determine which ones need to be run on a regular basis. These jobs can be configured and enabled from the Scheduled Jobs page ( **Administer > System Settings > Scheduled Jobs** ). Jobs can also be executed manually from this page. However, for most sites it is best to run the required jobs from the server command line, often as one or more automatically scheduled "cron" jobs.

A "run frequency" can be set for each enabled job, allowing you to configure a single "master" cron task to trigger ALL enabled CiviCRM jobs whose next run is due. For example, you may want to run the CiviMail mailing scheduler every time your master cron task runs (which might be once a minute). However, you only want the Address geocoder job to run once a day. You can enable both jobs, and set the run frequency for the mailing scheduler to "Every time cron job is run", while setting the frequency for the Address geocoded to "Daily".

The format to enter the parameters for those jobs that have available parameters listed in the table below is **one parameter per line**. For example, for the Greetings Updater scheduled job, you can enter the following in the parameters textbox to update all Individual postal greetings:

```
ct=Individual
gt=postal_greeting
```

![](https://wiki.civicrm.org/confluence/download/attachments/86213745/params.jpg?version=1&modificationDate=1448829741000&api=v2)

To actually trigger all the enabled jobs in Scheduled Jobs you will have to set up a cron job on the server that does this. The main system call is job.execute. The System Status message (/civicrm/a#/status) will only report "Cron running OK" if your cron job invokes job.execute. The System Status page will complain that cron is "not running" if your cron only invokes particular jobs such as 'process_mailing' or 'fetch_bounces'. Details for invoking job.execute are found below in the section "Command-line Syntax for Running Jobs".

**Note that the parameters for the relevant scheduled jobs should be returned by running getfields on the relevant job. We really want the UI to leverage these getfields results for dynamic documentation**

**Note as of CiviCRM version 4.7 the Deprecated Bin Scripts have been removed**

### Scheduled Jobs

| Component | Job Title (API action) | Description | Parameters | Deprecated Bin Script |
| --- | --- | --- | --- | --- |
| Core and CiviCase | Activity Processor/Process Inbound Emails ( **fetch_activities** ) | Inserts activities for a contact or a case by retrieving inbound emails from a mail directory | (none) | Email2Activity.php |
| Core | Geocode and Parse Addresses ( **geocode** ) | Retrieves geocodes (lat and long) and/or parses street addresses (populates street number, street name, etc.) in bulk. Geocoding provider must be configured (Administer > System Settings > Mapping and Geocoding). Street Address Parsing must be enabled (Administer > Localization > Address Settings). | geocoding=1 (0 to skip geocoding)
 parse=1 (0 to skip address parsing)
 start=null (optional, begin processing with this contact ID)
 end=null (optional, process contacts with ID's <= this value)
 throttle=0 (1 to add a 5 second sleep between each geocoding request) | UpdateAddress.php |
| Core | Clean-up Temporary Data and Files ( **cleanup** ) | Removes temporary data and files, and clears old data from cache tables. Recommend running this job every hour to help prevent database and file system bloat. | (none) | |
| Core | Disable expired relationships (**disable_expired_relationships)** | Disables relationships that have expired (ie. those relationships whose end date is in the past). | (none) | |
| Core | Greetings Updater ( **update_greeting** ) | Updates Email Greeting, Postal Greeting and / or Postal Addressee for contacts based on a passed in format or your site defaults found in Administer > Communications > Email Greeting Formats and Administer > Communications > Postal Greeting Formats. **This job is disabled by default and can not be enabled to run automatically as part of a scheduled jobs run. It can only be run manually (single job) from the Scheduled Jobs page or command line.** ([learn more...](https://wiki.civicrm.org/confluence/display/CRMDOC/Update+Greetings+and+Address+Data+for+Contacts)) | ct= (required: Individual, Organization, or Household)
 gt= (required: email_greeting, postal_greeting, or addressee)
 id= (optional: force script to set a greeting or addressee format other than default for a given contact type, use ID of format option value)
 force=0 (1 to update all contacts, otherwise only contacts with null values are updated) | UpdateGreeting.php |
| Core | Mail Reports ( **mail_report** ) | Generates and sends a copy of the specified report instance to the email addresses configured in that instance's Report Settings. | instanceId= (required, ID of report instance to send)
 format=csv (optional, to output the report as a CSV file instead of default PDF format)
 format=print (optional, to output the report as printer-friendly HTML)
 sendmail=0 (optional, tells the job NOT to email the report. use this in combination with print or csv format to write report to stdout so you can save it to a disk file) | CiviReportMail.php |
| Core | Rebuild Smart Group Cache ( **group_rebuild** ) | Rebuilds the smart group cache. | limit=Number optional-Limit the number of smart groups rebuild | |
| Core(?) | Send Scheduled SMS ( **process_sms** ) | Sends out scheduled SMS | (none) | |
| Core and CiviEvent | Send Scheduled Reminders ( **send_reminder** ) | Sends out scheduled reminders related to events or activities via email | (none) | action.cronjob.php |
| CiviCampaign | Process Survey Respondents ( **process_respondent** ) | Releases reserved survey respondents when they have been reserved for longer than the "Release Frequency" days specified for that survey. | (none) | RespondentProcessor.php |
| CiviEvent | Update Participant Statuses ( **process_participant** ) | Updates participant statuses for Wait-list and Approval Required event registration features. Moves participants from "On waitlist" to "Pending from waitlist" as space becomes available. Moves any type of Pending registrations to "Expired" if expiration period is set for an event. Sends notifications of status changes to participants. | (none) | ParticipantProcessor.php |
| CiviMail | Mailings scheduler ( **process_mailing** ) | Sends queued/scheduled CiviMail mailings | (none) | civimail.cronjob.php |
| CiviMail | Fetch Bounces ( **fetch_bounces** ) | Processes bounced email from the return channel. Fetches bounces from mailings and writes them to mailing statistics. | (none) | EmailProcessor.php |
| CiviMail | Validate Email Address from Mailing ( **update_email_resetdate** ) | Updates the reset_date on an email address to indicate that there was a valid delivery to this email address. | minDays=x
 maxDays=x
 Consider mailings that have completed between minDays and maxDays.
 Use one parameter per line. For example -
 minDays='1'
 maxDays='4' | |
| CiviMember | Update Membership Statuses and Send Renewal Reminders ( **process_membership** ) | Updates membership statuses and optionally sends renewal reminders When renewal reminders are sent, the member's reminder date field will be set to NULL in the database and on screen. When the membership is renewed, the field is populated with the updated renewal reminder date. | | UpdateMembershipRecord.php |
| CiviMember | Set Renewal Reminder Dates ( **process_membership_reminder_date** ) | Sets membership renewal reminder dates for current membership records where reminder date is null. **This job should never be run automatically as it will cause members to get renewal reminders repeatedly. It is disabled by default to prevent it from being included in an "run all jobs" session.** | (none) | UpdateMembershipReminderDate.php |
| CiviPledge | Process Pledges ( **process_pledge** ) | Updates pledge payment and pledge statuses and optionally sends payment reminders | send_reminders=1 (optional: add this parameter if you want pledge payment reminder emails sent) | UpdatePledgeRecord.php |

### Scheduled Jobs Editing Fields

Creating a Scheduled Job requires filling in API fields that are similar to those used in the API Explorer. [Find out more about using the API here](https://wiki.civicrm.org/confluence/display/CRMDOC/API+Reference) and [detailed instructions (developer oriented) here](https://wiki.civicrm.org/confluence/display/CRMDOC/How+to+migrate+or+write+an+api).

The API fields are:

* **Field 1: API -** civicrm_api3 (occasionally you may need to use civicrm_api2)
* **Field 2: API name -** this is a drop-down list and corresponds to the filename of files in the _civicrm/api/v3_ directory, ie, Activity in the list corresponds to file Activity.php in the directory.
* **Field 3: Action -** typically get, create, getvalue, update, delete, etc. See the list of typical actions in the documentation, but note that each API may define its own actions. Documentation for APIs and actions is here, but you may need to examine the files in the civicrm/api/v3 directory for the most detailed information available about APIs, actions, and options.
* **Field 4: Command parameters -** This is a list of parameters and values, like

entity_id=102
 contact_id=12932
 limit=10
 throttle=1
 list=0
 type=text

Again you'll need to read the [API reference](https://wiki.civicrm.org/confluence/display/CRMDOC/API+Reference) or check the documentation in the files themselves for details about available command parameters and their effects.

The result of this entry is a scheduled call to the API function defined by the fields. For example, if your fields were:

civicrm_api3 Activity get _with parameter_ contact_id=352

The result would be a call to function civicrm_api3_activity_get ($params) in file civicrm/api3/Activity.php, where $params=array(contact_id->532).

You can explore APIs, actions, and parameters at the API explorer, which is included in your CiviCRM installation at _yourwebsite.com/civicrm/api/explorer._ [An explanation and video tour of the API explorer is here.](http://civicrm.org/API_version_3) Once you have a working API call at the API explorer you can transfer the same API call, action, and parameters to create a new Scheduled Job that will carry out that same task automatically.

### Command-line Syntax for Running Jobs

#### Drush method

Using Drush, you require:

* the site path and URL, or a Drush alias
* the Drupal user (name or UID) you'll run the job as (requires appropriate permissions to access contact data)

Three examples below (bare minimum, using an alias and using Drush parameters).

```
drush civicrm-api -u 1 job.execute
```

```
drush @example.org -u civicrm.cron job.execute
```

```
drush -r /var/www/example.org -l https://example.org civicrm-api -u civicrm.cron job.execute
```

If successful, this command will output an array with a key/value of error = 0. In addition to the Drupal user permissions, you may also need to pay attention to the file permission of the linux user that the command runs as (e.g. typically run this command as the same user as apache - "apache", "nobody", or "www-data" are the most likely candidates). The final step to adding a drush command to your system cron is to add the --quiet option to drush. This is to suppress the non-error output that would otherwise fill up your cron mail feedback.

For more information using drush with cron see: [http://www.drush.org/en/master/cron/](http://www.drush.org/en/master/cron/)

#### wp-cli method

```
/path/to/wp-cli --user=<cron> --url=<http://example.com> --path=</path/to/wp> civicrm api job.execute auth=0
```

Replace <cron> with the WordPress user you will run the cron job as make certain that this user that has the needed permissions. Replace <http://example.com> with the url of your site. Replace </path/to/wp civicrm> with the path to your WordPress root folder

Se wp-cli documentation for a full list of commands. [http://wp-cli.org/](http://wp-cli.org/)

One to note here

1. With --skip-plugins=<plugin> This will allow you to stop a plugin from loading. This is needed if a certain plugin causes the cron job to fail. Below is the syntax to do that:

```
/path/to/wp-cli --user=<cron> --url=<http://example.com> --path=</path/to/wp> --skip-plugins=<plugin> civicrm api job.execute auth=0
```

1. With --timezone=<timezone identifier> option you can control the timezone(TZ) via wp-cli command (otherwise by default it will consider the TZ set in your wordpress site). Check the valid TZ identifiers [here](http://php.net/manual/en/timezones.php). Below is the syntax to do that:

```
/path/to/wp-cli --user=<cron> --url=<http://example.com> --path=</path/to/wp> --timezone=<timezone identifier> civicrm api job.execute auth=0
```

#### PHP cli method

This method requires a valid Username and Password (for a Drupal, Joomla or WordPress user who has adequate permissions for the job or jobs being run). If you use drupal, use the drush method, that is easier to configure and more secure (it doesn't require to put the user password)

**Typical set up: Use [Cron](https://en.wikipedia.org/wiki/Cron)**  **to give CiviCRM the chance to process enabled scheduled jobs according to the frequencies set.** e.g. every 15 minutes is a reasonable poll time. Here's a template line you might use in your user crontab:

```
*/15 * * * * /path/to/php /path/to/civcrm/module/bin/cli.php -s site -u user -p password -e Job -a execute
```

The **-s** parameter is the site name, and defaults to 'localhost'. It should be set the FQDN (fully qualified domain name) of your site (eg, 'example.com') Some jobs rely on this being set (in certain cases, the CiviMail mailing scheduler uses this FQDN to generate absolute URLs when relative URLs are in the email).

To run ALL scheduled jobs that are Enabled and are due to run based on last run and run frequency as a one-off from the command-line:

```
$ php /path/to/civicrm/bin/cli.php -s site -u user -p password -e Job -a execute
```

To run a specific job (regardless of Enabled status or last run time):

```
$ php bin/cli.php -s site -u user -p password -e Job -a <api action> --<params>
```

For example, to run Greeting updater job which requires ct (contact type) and gt (greeting type) parameters

```
$ php bin/cli.php -s site -u user -p password -e Job -a update_greeting --ct=Individual --gt=email_greeting
```

!!! tip "Enable Logging with cli.php for single jobs"

    Entries are ALWAYS written to the job log when you use 'execute' action to run all scheduled jobs. Single jobs run via cli.php do not write to the Job Log by default. If you want a single job run to be logged, add -j to the beginning of the command line.

    ```
    $ php bin/cli.php -j -s site -u user -p password -e Job -a process_mailing
    ```


!!! warning

    Make sure there that there are no **reserved or unsafe URL characters** in your username or password. These include:

    ```
    & = + $ ? % , / : { } | ' #
    ```

    and spaces.


#### URL method

You can run ALL scheduled jobs that are Enabled and are due to run based on last run and run frequency by invoking a URL with curl or wget. You can also invoke specific jobs via URL. This method requires a valid Username and Password (for a Drupal, Joomla or WordPress user who has adequate permissions for the job or jobs being run. The minimal permissions for that user are: "view all contacts", "access CiviCRM", "access CiviMail"). It also requires you to pass in the CIVICRM_SITE_KEY which is configured by editing your copy of civicrm.settings.php (see Configuring your Site Key below).

!!! tip "More Info on Using WGET or CURL"

    [Running Command-line Scripts via URL](https://wiki.civicrm.org/confluence/display/CRMDOC/Running+Command-line+Scripts+via+URL)


!!! tip "Logging with URL Method"

    Any jobs that are invoked using the URL method are automatically logged.


##### Run ALL Scheduled Jobs via URL

Calling cron.php as shown below will run ALL scheduled jobs that are Enabled and are due to run based on last run and run frequency:

In Drupal:

```
http://example.org/sites/all/modules/civicrm/bin/cron.php?name=username&pass=password&key=site-key
```

In Joomla:

```
http://example.org/administrator/components/com_civicrm/civicrm/bin/cron.php?name=username&pass=password&key=site-key
```

In WordPress:

```
http://example.org/[CONTENT-DIR]/plugins/civicrm/civicrm/bin/cron.php?name=username&pass=password&key=site-key
```

When calling these urls using wget from within a crontab, enclose the url in single quotes, like so:

```
*/15 * * * * wget -O - -q -t 1 'http://example.org/sites/all/modules/civicrm/bin/cron.php?name=username&pass=password&key=site-key'
```

An alternative way to set up cron job is using webcron like [https://www.easycron.com](https://www.easycron.com).

##### Run a Specific Job via URL

You can also run any single job via URL by passing job=<api_action> as well as any required parameters. For example to run the Update Greeting job via URL:

In Drupal:

```
http://example.org/sites/all/modules/civicrm/bin/cron.php?job=update_greeting&ct=Individual&gt=email_greeting&name=username&pass=password&key=site-key
```

In Joomla:

```
http://example.org/administrator/components/com_civicrm/civicrm/bin/cron.php?job=update_greeting&ct=Individual&gt=email_greeting&name=username&pass=password&key=site-key
```

In WordPress:

```
http://example.org/[CONTENT-DIR]/plugins/civicrm/civicrm//bin/cron.php?job=update_greeting&ct=Individual&gt=email_greeting&name=username&pass=password&key=site-key
```

##### Specific Job via URL works only for API entity = job

Note that you can execute API actions **_only_** from the API entity "job"; this is hard-coded into cron.php. So you can't call any of the built in API functions (other than those under entity "job") using cron.php, but you also can't create a new API action So for instance, if you create new API entity called "myapi" with API action "my action" then you would be able to call that action via the Scheduled Jobs page but you will not be able to call it via cron.php.

A possible workaround for this problem is here: [http://forum.civicrm.org/index.php/topic,28215.0.html](http://forum.civicrm.org/index.php/topic,28215.0.html)

#### Running CiviCRM crons in Aegir

If you are using the [Aegir](http://www.aegirproject.org) hosting environment, with the [hosting_civicrm](http://drupal.org/project/hosting_civicrm) module, the CiviCRM cron will automatically be configured. A global default cron interval is defined in the CiviCRM cron queue settings. It is also possible to set a site-specific value. 

You can also use the Drush method below to call CiviCRM cron tasks manually.

#### Running CiviCRM crons using Drush

You can run cron manually for a site by using the drush command:

```
drush @example.org -u admin cvapi Job.execute
```

This assumes that you have an administrator account called "admin" with the correct CiviCRM permissions.

Currently (CiviCRM 4.2.x) you need to use an additional argument, 'auth=0', to work around [CRM-11199](http://issues.civicrm.org/jira/browse/CRM-11199).

### Configuring your Site Key

If you are triggering jobs using the URL method, you must manually generate a site key, and then add it to your CiviCRM settings file.

* First generate the key. We recommend using a 16-32 bit alphanumeric/punctuation key value. You can ...
    * create a random 16-32 character key from your keyboard OR
    * use an external service like: [http://www.thebitmill.com/tools/password.html](http://www.thebitmill.com/tools/password.html) OR
    * use the following shell command to generate a key

        ```
        $ php -r 'echo md5(uniqid(rand(), true)) . " ";'
        ```

    !!! warning
    
        Make sure there that there are no **reserved or unsafe URL characters** in your site key. These include:
        
        ```
        & = + $ ? % , / : { } # | '
        ```
        and spaces.

* Open your settings file - `civicrm.settings.php` - in your favorite editor and either add or edit the `CIVICRM_SITE_KEY` value.
    
    * **in Drupal** , civicrm.settings.php is located in /sites/default
    * **in Joomla,** there are 2 civicrm.settings.php files. By default these are located in site/administrator/components/com_civicrm and site/components/com_civicrm - it's important to add the same key to both files as some Joomla front-end functions rely on the key (eg, decrypting the Outbound Mail SMTP Password)
    * **in WordPress** , civicrm.settings.php is located in : For versions 4.6 and Prior `/wp-content/plugins/civicrm`. Starting in 4.7 for new installs `CONTENT-DIR/uploads/civicrm`


    Depending on which version of CiviCRM you've installed, you may have this setting in your file already with a value of _null_, or you may need to add the line. In either case, replace _null_ with the value of your key.
    
    EXAMPLE: If my generated site key value is: `3cx4aNkpQwxmM1hTMV~!B09iO6` then my settings file should look like this:
    
    ```
    define( 'CIVICRM_SITE_KEY', '3cx4aNkpQwxmM1hTMV~!B09iO6' );
    ```

* Finally, make sure that you include the key=<sitekey> parameter whenever your run any of these scripts.

## Troubleshooting

### DB Error: Connect failed

The connect failed error message means that CiviCRM was not able to connect to the SQL database at all. The problem may be in connecting to the CiviCRM database OR with the Drupal/Joomla/WordPress database. If your CiviCRM installation is otherwise working, the most likely cause is the connection to the Drupal/Joomla/WordPress database, which is accessed much less frequently.

The most likely cause is incorrect username, password, or other settings in the civicrm.settings.php file. Carefully check the setting for _define( 'CIVICRM_UF_DSN'_ - your setting may have been incorrect for some time and enabling the cron process is only now exposing the problem.

### DB Error: no such table

A common cause of this problem is if your Drupal installation has a table prefix. To validate the connection, CiviCRM looks at the _users_ table while initializing the cron job. The specific name of the users table is set in _yourpath/civicrm/admin/setting/uf_ (or civicrm/administer/system settings/CMS database integration). If the users table name set there does not match the actual table name in your Drupal SQL database you will get the 'no such table' error. Check your Drupal table prefix in settings.php (usually in sites/default) - look for a setting like _$db_prefix = 'dru_';_. In _yourpath/civicrm/admin/setting/uf_ change the "Drupal Users Table Name" to match–for instance, if your prefix is _dru__, change the table name to _dru_users_.

[More information on this issue is here - check the comments](http://issues.civicrm.org/jira/browse/CRM-7471?page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel).

### Wrong permissions for cli.php

If you are running cli.php directly via cron job you may need to change permissions of cli.php to allow it to execute. Depending on how cron runs and the owner of cli.php you might try one of these options–or use your ftp program to change file permission for cli.php similarly:

```
chmod 744 cli.php [Gives owner execute permissions]
```

```
chmod 754 cli.php [Gives owner & group execute permissions]
```

```
chmod 755 cli.php [Gives everyone execute permissions]
```

Running cli.php via php (as recommended above) will also solve this problem. Use this:

```
php /your/directory/structure/sites/all/modules/civicrm/bin/cli.php -u user -p password -e Job -a execute
```

Rather than this:

```
/your/directory/structure/sites/all/modules/civicrm/bin/cli.php -u user -p password -e Job -a execute
```

Also, some systems will require the cli version (e.g. /usr/bin/php-cli vs /usr/bin/php)

### Using the URL method with https and cron fails silently

If your server has an older version of wget installed and you are trying to connect via https there are a couple of problems that can cause wget to exit.

* wget doesn't support Server Name Indication (SNI) ([Redhat bug id 909604](https://bugzilla.redhat.com/show_bug.cgi?id=909604), [Debian bug id 737328](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=737328))
* wget doesn't support Server Alternate Names (pretty old problem) ([Debian bug id 409938](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=409938))

The solution is to upgrade wget to a version with the proper https support. A workaround is to use the flag --no-check-certificate in your wget call.

```
*/15 * * * * wget --no-check-certificate -O - -q -t 1 'http://example.org/sites/all/modules/civicrm/bin/cron.php?name=username&pass=password&key=site-key'
```

### Other issues

[Two](http://forum.civicrm.org/index.php?topic=23859.0) [threads](http://forum.civicrm.org/index.php?topic=24926.0) in the CiviCRM Forums have ideas for troubleshooting problems with the scheduled jobs. If you find specific problems or solutions, please make note of them here.

If you've just upgraded to CiviCRM 4.3 on a multi-site install and your cron silently stops working, try switching to the Drush method ([forum thread](http://forum.civicrm.org/index.php/topic,30338.0.html)).

If you're using HostGator, and switched to PHP 5.3, check [this thread](http://forum.civicrm.org/index.php?topic=26334.0).


## Running Command-line Scripts via URL

CiviCRM scheduled jobs (background processing tasks) can be run using the HTTP **GET** or **POST** methods. This is an alternative to using PHP Cli which is [documented here](https://wiki.civicrm.org/confluence/display/CRMDOC/Managing+Scheduled+Jobs). The GET method is slightly easier to configure, but may be considered less secure since authentication information is included in the command line, and therefore may be visible to other users with sufficient permissions on your server. Examples of both methods are included below. Information for run

!!! warning

    Make sure there that there are no **reserved or unsafe URL characters** in your username or password. These include:

    ```
    & = + $ ? % , / : { } | ' #
    ```

    and spaces.


!!! warning

    Make sure that the user that is referred in commands below has **correct permissions**. At minimum those are:

    * view all contacts
    * access CiviCRM
    * access CiviMail

### Run Scheduled Jobs using POST Method

#### EXAMPLE: Use webcron that supports POST method (Drupal installation) - Easier way

1) Sign up an account on [https://www.easycron.com](https://www.easycron.com).

2) In the URL field of the Create Cron Job form, enter:

```
http://www.example.org/sites/all/modules/civicrm/bin/cron.php
```

3) In the POST field, enter:

```
name=username&pass=password&key=site-key&job=process_mailing
```

When using the POST method with other cron job methods below, the username and password values are written to a text file, and then that filename is specified when running the script.

#### EXAMPLE: Use "wget" to send queued CiviMail mailings (Drupal installation)

1) Create a file named civicrm-wgetrc that contains this line (replacing _username_, _password_ and _site-key_ with the actual values).

```
post-data=name=username&pass=password&key=site-key&job=process_mailing
```

2) Create a script that contains these lines:

```
export WGETRC=civicrm-wgetrc
wget -O - -q -t 1 \
 http://www.example.org/sites/all/modules/civicrm/bin/cron.php
```

#### EXAMPLE: Use [curl](http://curl.haxx.se/) to send queued CiviMail mailings (Drupal installation)

1) Create a file named civicrm-curl that contains this line (replacing _<username>_, _<password>_ and _<site-key>_ with the actual values).

```
name=<username>&pass=<password>&key=site-key&job=process_mailing
```

2) Create a script that contains this command:

```
curl --data @civicrm-curl \
 http://www.example.org/sites/all/modules/civicrm/bin/cron.php
```

#### ALTERNATIVE curl calling syntax

```
CIVI_USER=civicron
CIVI_PASS=xxxxxxxx
SITE_KEY=yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy

CIVI_CRON="http://www.example.org/sites/all/modules/civicrm/bin/cron.php"
POST_AUTH="-d name=${CIVI_USER} -d pass=${CIVI_PASS} -d key=${SITE_KEY}"
POST_JOBS="-d job=process_mailing"

curl -s -o civicrm.cronjob.out ${POST_AUTH} ${POST_JOBS} ${CIVI_CRON}
```

Check the [curl documentation](http://curl.haxx.se/docs/) for information about logging and console output options.

### Run Scheduled Jobs using GET Method

When using the GET method, the username, password and site-key values are passed in the URL (as well as any required job-specific parameters).

#### EXAMPLE: Use "wget" to run the Update Membership script - replacing _<username>_, _<password>_ and _<site-key>_ with the actual values (Drupal installation)

```
$ wget 'http://example.org/sites/all/modules/civicrm/bin/cron.php?name=<username>&pass=<password>&key=<sitekey>&job=process_membership'
```

#### EXAMPLE: Use "[curl](http://curl.haxx.se/)" to run the Update Membership script - replacing _<username>_, _<password>_ and _<site-key>_ with the actual values (Drupal installation)

```
$ curl 'http://example.org/sites/all/modules/civicrm/bin/cron.php?name=<username>&pass=<password>&key=<sitekey>&job=process_membership'
```

#### EXAMPLE: Use webcron (Drupal installation)

1) Sign up an account on [https://www.easycron.com](https://www.easycron.com/).

2) In the URL field of the Create Cron Job form, enter:

```
http://example.org/sites/all/modules/civicrm/bin/cron.php?name=<username>&pass=<password>&key=<sitekey>&job=process_membership
```

Check the [curl documentation](http://curl.haxx.se/docs/) for information about logging and console output options.

### Example CRON

A cron can be configured to run either a script in a directory or to use WGET. This example is just one of the many ways to configure a cron, which uses WGET to run the CiviCRM consolidated schedule job every 5 minutes for a Drupal + CiviCRM installation. The wget flags ( -O -q -t etc ) discard the output thereby running the wget silently. Note that this method saves your username and password in the crontab itself and may not be suitable in some situations.

1. Login to your server using SSH, or via your control panel
1. Access cron by:

    1. _crontab -e_
    1. _env EDITOR=nano crontab -e_
    1. click the Cron Job icon in your control panel
1. Use the example below as a **single line** in the cron, replacing your actual domain, username, password and key
1. Notice the use of **single** quotes around the url-this is necessary if the query string contains an ampersand (&)
1. Save
1. Wait a few minutes and check if the scheduled jobs have executed properly

```
*/5 * * * * wget -O /dev/null -q -t 1 'http://example.org/sites/all/modules/civicrm/bin/cron.php?name=<name>&pass=<pass>&key=<key>'
```

The above example caters for Drupal; the following example caters for WordPress. The cron job is set to run every 15 minutes, and the wget uses IPv4 only with TLS.

```
*/15 * * * * wget -O - -4 -q -t 1 'http://example.org/[wp content]/plugins/civicrm/civicrm/bin/cron.php?name=<user>&pass=<password>&key=<key>'
```

### Cron.php will run jobs with API entity=job only

Note that you can execute API actions **_only_** from the API entity "job"; this is hard-coded into cron.php. So you can't call any of the built in API functions (other than those under entity "job") using cron.php, but you also can't create a new API action So for instance, if you create new API entity called "myapi" with API action "my action" then you would be able to call that action via the Scheduled Jobs page but you will not be able to call it via cron.php.

A possible workaround for this problem is here: [http://forum.civicrm.org/index.php/topic,28215.0.html](http://forum.civicrm.org/index.php/topic,28215.0.html)
