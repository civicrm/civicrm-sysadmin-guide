# Scheduled jobs

This page provides instructions on setting up scheduled jobs to run automatically.

!!! tip "More info in the User Guide"
    See the [scheduled jobs](https://docs.civicrm.org/user/en/latest/initial-set-up/scheduled-jobs) page in the User Guide to learn more about how scheduled jobs work and why they are important.
    
## Process overview

A typical CiviCRM installation uses the following process to execute scheduled jobs: 

1. A CiviCRM administrative user configures scheduled jobs via CiviCRM's UI.
1. A cron job (outside of CiviCRM) executes CiviCRM's `Job.execute` API call regularly (e.g. every 5 minutes).
1. The `Job.execute` API call runs all the configured scheduled jobs which are necessary at that point in time. 
1. Each scheduled job executes its own API call (e.g. `Job.geocode`) and passes any parameters to this call as configured in the UI.

## Setup overview

To get scheduled jobs working you'll need to perform the following setup tasks:

1. Configure and enable at least one scheduled job from within the UI. _([Instructions in the User Guide](https://docs.civicrm.org/user/en/latest/initial-set-up/scheduled-jobs/#configuring))_

    * For example, a default job exists in new CiviCRM installations called "CiviCRM Update Check" which runs `Job.version_check`. This is an easy job to use while testing your schedule jobs setup. For the purposes of testing your setup, it will be helpful to modify the frequency of this job so that it runs every time cron runs.
    
1. Manually execute your scheduled job from within the UI to ensure that it completes without error. View the job log if necessary. _([Instructions in the User Guide](https://docs.civicrm.org/user/en/latest/initial-set-up/scheduled-jobs/#manual-execution))_

1. Choose a method of executing the `Job.execute` API call from the command line. _([Instructions below](#choosing-a-method))_

    1. Test that this method works by running the command manually and viewing the job log again.

1. Set up a cron job to run the command regularly. _([Instructions below](#cron))_

    1. Test that your cron job works by viewing the job log after it should have run.
    
1. Choose more scheduled jobs to enable to meet your needs. _([Instructions in the User Guide](https://docs.civicrm.org/user/en/latest/initial-set-up/scheduled-jobs/#specific-jobs))_


## Preparing 

### Choosing a command {:#choosing-a-method}

Due to the flexibility of CiviCRM's API, there are several different methods for executing the `Job.execute` API call from the command line. Here are some guidelines for choosing the best method for your situation:

<!-- TODO: finish writing these steps, or maybe re-write them -->

1. If you are using Drupal, then choose the [drush method](#drush).
1. If you are using Wordpress, then choose the [wp-cli method](#wp-cli).
1. If you are using Joomla, then ... ???

### Choosing an OS user

When you run one of the commands below to execute `Job.execute`, it's important to pay attention to the file permission of the operating system user that the command runs as. Typically you'll want to run the command as the same user as Apache (i.e. `www-data`, `apache`, or `nobody` are the most likely candidates).

### Choosing a CMS user

Some of these methods require a valid username and password (for a CMS user who has adequate permissions for the job or jobs being run).

* Make sure there that there are no reserved or unsafe URL characters in your username or password. These include spaces as well as the following characters:

    ```
    & = + $ ? % , / : { } | ' #
    ```

* Make sure that the user that is referred in commands below has **correct permissions**. At minimum those are:
    * View all contacts
    * Access CiviCRM
    * Access CiviMail
        
## Commands to call `Job.execute` {:#commands}

### cv method {:#cv}

<!-- TODO: describe how to use `cv` to run `job.execute` -->

https://github.com/civicrm/cv

### Drush method {:#drush}

```
$ drush \
  -r /var/www/example.org \
  -l https://example.org \
  -u myusername \
  civicrm-api job.execute
```

Notes:

* `drush` only works for Drupal sites.
* See [drush.org](http://www.drush.org/) for more info about this tool.
* If successful, this command will output a key/value array with a `error = 0`.
* The final step to adding a Drush command to your system cron is to add the `--quiet` option to Drush. This is to suppress the non-error output that would otherwise fill up your cron mail feedback.

### wp-cli method {:#wp-cli}

```
$ /path/to/wp-cli \
  --user=myusername \
  --url=http://example.org \
  --path=/var/www/example.org/ \
  civicrm api job.execute auth=0
```

Notes: 

* `wp-cli` only works for WordPress
* See [wp-cli.org](http://wp-cli.org/) for more info about this tool.
* Add the `--skip-plugins=<plugin>` option to stop a plugin from loading. This is needed if a certain plugin causes the cron job to fail.
* Add the `--timezone=<timezone_identifier>` option to control the timezone. If you don't do this, then `wp-cli` will use the timezone set in your wordpress site. Here are the [valid timezone identifiers](http://php.net/manual/en/timezones.php).

### cli.php method {:#php-cli}

```
$ /path/to/php /path/to/civcrm/bin/cli.php \
  -s example.org \
  -u myusername \
  -p mypassword \
  -e Job \
  -a execute
```

Notes:

* The `-s` parameter is the site name, and defaults to `localhost`. It should be set to the FQDN (fully qualified domain name) of your site (eg, `example.org`). Some jobs rely on this being set (in certain cases, the CiviMail mailing scheduler uses this FQDN to generate absolute URLs when relative URLs are in the email).

### HTTP method {:#http}

<!-- TODO: write introduction to HTTP methods -->

#### URL to `cron.php`

* In Drupal:
    
    ```
    https://example.org/sites/all/modules/civicrm/bin/cron.php
    ```

* In Joomla:

    ```
    https://example.org/administrator/components/com_civicrm/civicrm/bin/cron.php
    ```

* In WordPress:

    ```
    https://example.org/<CONTENT-DIR>/plugins/civicrm/civicrm/bin/cron.php
    ```
    
    (Make sure to replace `<CONTENT-DIR>` with your own value.)

#### Parameters to pass to `cron.php`

* This method requires you to pass in your [site key](/setup/site-key.md) as shown in the examples below.
* When using `cron.php` you can only execute API from "job" entity. This means that if an extension has defined an API action like `Foo.process` you won't be able to call this action from `cron.php` because its entity is `Foo` instead of `Job`.

#### GET vs POST

The GET method is slightly easier to configure, but may be considered less secure since authentication information is included in the command line, and therefore may be visible to other users with sufficient permissions on your server.

#### Calling `cron.php` with `wget`

When calling these urls using `wget`, enclose the url in single quotes, like so:

```
$ wget -O - -q -t 1 'http://example.org/sites/all/modules/civicrm/bin/cron.php?name=username&pass=password&key=site-key'
```

Use "wget" to send queued CiviMail mailings (Drupal installation)

1. Create a file named `civicrm-wgetrc` that contains this line (replacing `username`, `password` and `site-key` with the actual values).
    
    ```
    post-data=name=username&pass=password&key=site-key&job=process_mailing
    ```

1. Create a script that contains these lines:
    
    ```
    export WGETRC=civicrm-wgetrc
    wget -O - -q -t 1 \
      http://example.org/sites/all/modules/civicrm/bin/cron.php
    ```

#### Calling `cron.php` with `curl`

Use [curl](http://curl.haxx.se/) to send queued CiviMail mailings (Drupal installation)

1. Create a file named `civicrm-curl` that contains this line (replacing `<username>`, `<password>` and `<site-key>` with the actual values).

    ```
    name=<username>&pass=<password>&key=site-key&job=process_mailing
    ```

1. Create a script that contains this command:

    ```
    curl --data @civicrm-curl \
     http://example.org/sites/all/modules/civicrm/bin/cron.php
    ```

Alternative curl calling syntax

```
CIVI_USER=civicron
CIVI_PASS=xxxxxxxx
SITE_KEY=yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy

CIVI_CRON="http://example.org/sites/all/modules/civicrm/bin/cron.php"
POST_AUTH="-d name=${CIVI_USER} -d pass=${CIVI_PASS} -d key=${SITE_KEY}"
POST_JOBS="-d job=process_mailing"

curl -s -o civicrm.cronjob.out ${POST_AUTH} ${POST_JOBS} ${CIVI_CRON}
```

Check the [curl documentation](http://curl.haxx.se/docs/) for information about logging and console output options.


## Cron setup {:#cron}

In order for the scheduled jobs configured within the CiviCRM UI to run automatically, you'll need to configure a cron job which executes the `Job.execute` API call.

!!! note
    The System Status screen `/civicrm/a#/status` will only report "Cron running OK" if your cron job invokes `job.execute`. The System Status page will complain that cron is "not running" if your cron only invokes particular jobs such as `process_mailing` or `fetch_bounces`. 
    
### crontab

<!-- TODO: clean up this section a bit -->

A cron can be configured to run either a script in a directory or to use `wget`. This example is just one of the many ways to configure a cron, which uses `wget` to run the CiviCRM consolidated schedule job every 5 minutes for a Drupal + CiviCRM installation. The wget flags ( -O -q -t etc ) discard the output thereby running the wget silently. Note that this method saves your username and password in the crontab itself and may not be suitable in some situations.

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
*/15 * * * * wget -O - -4 -q -t 1 'https://example.org/[wp content]/plugins/civicrm/civicrm/bin/cron.php?name=<user>&pass=<password>&key=<key>'
```
    
### webcron

Another way to set up your cron job is to use a webcron like [easycron](https://www.easycron.com). This example assumes Drupal as your CMS.

1. Sign up an account on [https://www.easycron.com](https://www.easycron.com).

1. In the URL field of the Create Cron Job form, enter:

    ```
    http://example.org/sites/all/modules/civicrm/bin/cron.php
    ```

1. In the POST field, enter:

    ```
    name=username&pass=password&key=site-key&job=process_mailing
    ```

### Aegir

If you are using the [Aegir](http://www.aegirproject.org) hosting environment, with the [provision_civicrm](http://drupal.org/project/provision_civicrm) sub-module, you can automate the installation of a new cron for each site using the [hosting_civicrm_cron](http://drupal.org/project/hosting_civicrm_cron) module. You can also use the Drush method below to call CiviCRM cron tasks.


## Running specific jobs via cron {:#specific-jobs-via-cron}

<!-- TODO: re-writethis section. Point to specific API calls documented in User Guide here: https://github.com/civicrm/civicrm-user-guide/pull/228 and describe how system administrators can use any of the above methods but swap out `Job.execute` for one of the other API calls. -->

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



!!! tip "Enable Logging with cli.php for single jobs"

    Entries are ALWAYS written to the job log when you use 'execute' action to run all scheduled jobs. Single jobs run via cli.php do not write to the Job Log by default. If you want a single job run to be logged, add -j to the beginning of the command line.

    ```
    $ php bin/cli.php -j -s site -u user -p password -e Job -a process_mailing
    ```


## Troubleshooting

### DB Error: Connect failed

The connect failed error message means that CiviCRM was not able to connect to the SQL database at all. Theproblem may be in connecting to the CiviCRM database OR with the Drupal/Joomla/WordPress database. If your CiviCRM installationis otherwise working, the most likely cause is the connection to the Drupal/Joomla/WordPress database, which is accessed much less frequently.

The most likely cause is incorrect username, password, or other settings in the `civicrm.settings.php` file. Search the settings file for `CIVICRM_UF_DSN` &mdash; your setting may have been incorrect for some time and enabling the cron process is only now exposing the problem.

### DB Error: no such table

A common cause of this problem is if your Drupal installation has a table prefix. To validate the connection, CiviCRM looks at the `users` table while initializing the cron job. The specific name of the `users` table is set in `yourpath/civicrm/admin/setting/uf` (or via the UI in **Administer > System Settings > CMS database integration**). If the setting for the `users` table name does not match the actual table name in your Drupal MySQL database, you will get the 'no such table' error. Check your Drupal table prefix in `settings.php` (usually in `sites/default`) - look for a setting like `$db_prefix = 'dru_';`. In `yourpath/civicrm/admin/setting/uf` change the "Drupal Users Table Name" to match–for instance, if your prefix is `dru`_, change the table name to `dru_users`. See [CRM-7471](http://issues.civicrm.org/jira/browse/CRM-7471?page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel) for more info on this error.

### Wrong permissions for `cli.php`

If you are running `cli.php` directly via cron job you may need to change permissions of `cli.php` to allow it to execute. Depending on how cron runs and the owner of `cli.php` you might try one of these options.

```
chmod +x cli.php
```

Running `cli.php` via php (recommended) will also solve this problem.

* Use this:

    ```
    php /your/directory/structure/sites/all/modules/civicrm/bin/cli.php -u user -p password -e Job -a execute
    ```

* Rather than this:

    ```
    /your/directory/structure/sites/all/modules/civicrm/bin/cli.php -u user -p password -e Job -a execute
    ```

Also, some systems will require the cli version (e.g. `/usr/bin/php-cli` vs `/usr/bin/php`)

### Using the URL method with https and cron fails silently

If your server has an older version of `wget` installed and you are trying to connect via https there are a couple of problems that can cause `wget` to exit.

* `wget` doesn't support Server Name Indication (SNI) ([Redhat bug id 909604](https://bugzilla.redhat.com/show_bug.cgi?id=909604), [Debian bug id 737328](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=737328))
* `wget` doesn't support Server Alternate Names (pretty old problem) ([Debian bug id 409938](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=409938))

The solution is to upgrade `wget` to a version with the proper https support. A workaround is to use the flag `--no-check-certificate` in your `wget` call.

```
wget --no-check-certificate -O - -q -t 1 'https://example.org/sites/all/modules/civicrm/bin/cron.php?name=username&pass=password&key=site-key'
```