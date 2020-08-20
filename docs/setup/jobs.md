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

Due to the flexibility of CiviCRM's API, there are several different methods ([listed below](#commands)) for executing the `Job.execute` API call from the command line. You will need to choose one.

### Choosing an OS user

In most situations, you will want to run the commands to execute `Job.execute` as the user that runs the web server (e.g. Apache).  This is typically `www-data`, `apache`, or `nobody`.

More advanced configurations may want to run the commands as other users.  CiviCRM needs read and write access to some directories to operate normally, and can experience issues if "run" by multiple users simultaneously. If your CiviCRM runs on the web as user `www-data`, you need either to execute CiviCRM CLI tasks as the same user *or* to make special provision to ensure filesystem permissions do not interfere with normal operation. See [this StackExchange question](https://civicrm.stackexchange.com/questions/2923/civicrm-does-not-have-permission-to-write-temp-files) for more discussion on this.

### Choosing a CMS user

Some of these methods require a valid username and password (for a CMS user who has adequate permissions for the job or jobs being run).  You can use an existing user but it is usually better to create a new user for this purpose - e.g. 'cronuser'.

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

[`cv`](https://github.com/civicrm/cv) is a CLI utility for CiviCRM, like [`drush`](http://www.drush.org/en/master/) is to Drupal, [`wp-cli`](http://wp-cli.org/) to WordPress, and [Joomlatools Console](https://www.joomlatools.com/developer/tools/console/) to Joomla. Once you have `cv` installed, you can execute CiviCRM's scheduled jobs with this command:

```
cv api job.execute --user=cronuser --cwd=/var/www/example.org
```

Notes:

* If `cv` only works when you run it from a shell and not from cron you may need to invoke it by calling it with an absolute path to php and to CV.

```example
/full/path/to/php /full/path/to/cv api job.execute --user=cronuser --cwd=/var/www/example.org
```

* Replace ```cronuser``` with the CMS user you chose in the previous step.
* Once you have it working, add the `--quiet` flag to silence non-error output.

### Drush method {:#drush}

```
$ drush \
  -r /var/www/example.org \
  -l https://example.org \
  -u cronuser \
  civicrm-api job.execute
```

Notes:

* `drush` only works for Drupal sites.
* See [drush.org](http://www.drush.org/) for more info about this tool.
* Replace ```cronuser``` with the CMS user you chose in the previous step.
* If successful, this command will output a key/value array with a `error = 0`.
* The final step to adding a Drush command to your system cron is to add the `--quiet` option to Drush. This is to suppress the non-error output that would otherwise fill up your cron mail feedback.

### wp-cli method {:#wp-cli}

```
$ /path/to/wp-cli \
  --user=cronuser \
  --url=http://example.org \
  --path=/var/www/example.org/ \
  civicrm api job.execute auth=0
```

Notes:

* `wp-cli` only works for WordPress
* See [wp-cli.org](http://wp-cli.org/) for more info about this tool.
* Replace ```cronuser``` with the CMS user you chose in the previous step.
* Add the `--skip-plugins=<plugin>` option to stop a plugin from loading. This is needed if a certain plugin causes the cron job to fail.
* Add the `--timezone=<timezone_identifier>` option to control the timezone. If you don't do this, then `wp-cli` will use the timezone set in your wordpress site. Here are the [valid timezone identifiers](http://php.net/manual/en/timezones.php).

### cli.php method {:#php-cli}

```bash
/path/to/php /path/to/civcrm/bin/cli.php \
  -s example.org \
  -u cronuser \
  -p cronuserpassword \
  -e Job \
  -a execute
```

Notes:

* The `-s` parameter is the site name, and defaults to `localhost`. It should be set to the FQDN (fully qualified domain name) of your site (eg, `example.org`). Some jobs rely on this being set (in certain cases, the CiviMail mailing scheduler uses this FQDN to generate absolute URLs when relative URLs are in the email).
* Replace ```cronuser``` and ```cronuserpassword``` with the CMS user and password you chose in the previous step.

### HTTP method {:#http}

If you don't have command line access to the server running CiviCRM, then you can set up a separate system to make HTTP requests to your server in order to execute the `Job.execute` API call.

You can use either the [REST interface](https://docs.civicrm.org/dev/en/latest/api/interfaces/#rest) (as documented in the Developer Gude) or the `cron.php` (as documented below).

#### URL to `cron.php`

The URL you need to call depends on the CMS you are using.  In the examples below, replace `<cron-url>` with one of the following (using `http` or `https` as appropriate).

* Drupal:
   
    ```
    https://example.org/sites/all/modules/civicrm/bin/cron.php
    ```

* Joomla:

    ```
    https://example.org/administrator/components/com_civicrm/civicrm/bin/cron.php
    ```

* WordPress:

    ```
    https://example.org/<CONTENT-DIR>/plugins/civicrm/civicrm/bin/cron.php
    ```
   
    (Make sure to replace `<CONTENT-DIR>` with your own value.)

#### Parameters to pass to `cron.php`

You will need to pass the following parameters to `cron.php`.  Replace as appropriate in the examples below.

* `<username>` = the CMS username - see [Choosing a CMS User](#choosing-a-cms-user)
* `<password>` = the pasword for the CMS user
* `<site-key>` = your site key - see [Configuring your site key](../setup/site-key.md)

#### GET vs POST

The GET method is slightly easier to configure, but may be considered less secure since authentication information is included in the command line, and therefore may be visible to other users with sufficient permissions on your server.

#### Calling `cron.php` with `wget`

When calling these urls using `wget`, enclose the url in single quotes, like so:

```bash
$ wget -O - -q -t 1 '<cron-url>?name=<username>&pass=<password>&key=<site-key>'
```

Use `wget` to send queued CiviMail mailings:

1. Create a file named `civicrm-wgetrc` that contains this line:
   
    ```
    post-data=name=<username>&pass=<password>&key=<site-key>&job=process_mailing
    ```

1. Create a script that contains these lines:
   
    ```bash
    export WGETRC=civicrm-wgetrc
    wget -O - -q -t 1 <cron-url>
    ```

#### Calling `cron.php` with `curl`

Use `curl` to send queued CiviMail mailings

1. Create a file named `civicrm-curl` that contains this line:

    ```
    name=<username>&pass=<password>&key=<site-key>&job=process_mailing
    ```

1. Create a script that contains this command:

    ```bash
    curl --data @civicrm-curl <cron-url>
    ```

Alternative curl calling syntax

```bash
CIVI_USER=<username>
CIVI_PASS=<password>
SITE_KEY=<site-key>

CIVI_CRON="<cron-url>"
POST_AUTH="-d name=${CIVI_USER} -d pass=${CIVI_PASS} -d key=${SITE_KEY}"
POST_JOBS="-d job=process_mailing"

curl -s -o civicrm.cronjob.out ${POST_AUTH} ${POST_JOBS} ${CIVI_CRON}
```

Check the [curl documentation](http://curl.haxx.se/docs/) for information about logging and console output options.


## Cron setup {:#cron}

In order for the scheduled jobs configured within the CiviCRM UI to run automatically, you'll need to configure a cron job which executes the `Job.execute` API call.

!!! note
    The System Status screen `/civicrm/a#/status` will only report "Cron running OK" if your cron job invokes `Job.execute`. The System Status page will complain that cron is "not running" if your cron only invokes particular jobs such as `process_mailing` or `fetch_bounces`.
   
### crontab {:#crontab}

1. Make sure you have chosen (and tested) one of the [commands to call `Job.execute`](#commands). In the following examples, we will refer to your command as `<command>`.
1. You need to edit the crontab of the OS User you determined earlier.

   a. If you are logged in as that user, run:

    ```bash
    crontab -e
    ```
    
   b. Alternatively, run:

    ```bash
    sudo crontab -e -u os_user
    ```

    and substitute the chosen OS user for os_user (eg 'www-data')
1. Place the following line in your crontab to run your command every 5 minutes.

    ```
    */5 * * * * <command>
    ```
   
### webcron

Another way to set up your cron job is to use a webcron like [easycron](https://www.easycron.com).

1. Sign up an account on [https://www.easycron.com](https://www.easycron.com).

1. In the URL field of the Create Cron Job form, enter the [URL for your CMS](#url-to-cronphp). For example for Drupal:

    ```
    http://example.org/sites/all/modules/civicrm/bin/cron.php
    ```

1. In the POST field, enter:

    ```
    name=<username>&pass=<password>&key=<site-key>&job=process_mailing
    ```
    (replacing `<username>`, `<password>` and `<site-key>` with the actual values)

### Aegir

If you are using the [Aegir](http://www.aegirproject.org) hosting environment, with the [provision_civicrm](http://drupal.org/project/provision_civicrm) sub-module, you can automate the installation of a new cron for each site using the [hosting_civicrm_cron](http://drupal.org/project/hosting_civicrm_cron) module. You can also use the Drush method below to call CiviCRM cron tasks.


## Running specific jobs via cron {:#specific-jobs-via-cron}

In the [commands](#commands) section above, we describe the various methods for executing the `Job.execute` API call, which in turn runs all the scheduled jobs necessary at that moment.

But if you want more control over the scheduling of your jobs you can use software like [crontab](#cronb) to execute specific jobs. For example, maybe you want to run `Job.mail_report` on the first Monday of the month. CiviCRM's Scheduled Jobs interface does not allow this level of granularity and control.

With all of the commands above, you can replace `Job.execute` with the API of the job of your choosing. Refer to the [list of all jobs](https://docs.civicrm.org/user/en/latest/initial-set-up/scheduled-jobs/#specific-jobs) in the User Guide to find the right API call.

When using `cron.php` you can only execute API from "job" entity. This means that if an extension has defined an API action like `Foo.process` you won't be able to call this action from `cron.php` because its entity is `Foo` instead of `Job`.

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

```bash
wget --no-check-certificate -O - -q -t 1 '<cron-url>?name=<username>&pass=<password>&key=<site-key>'
```
