# Detailed Logging

Detailed logging (aka "logging") allows you to maintain a full history of *all* changes made to *everything* in CiviCRM. It's a powerful feature which is turned *off* be default &mdash; because it's a bit overkill for most organizations, and it comes with some downsides too.

![Screenshot of report showing field differences within one update](/img/detailed-logging-report.png)


## How logging works

* Logging adds many new tables to your database &mdash; one new table for most existing CiviCRM tables in the database, each with a `log_` prefix (cache tables are ignored).

* Optionally (and recommended), you can choose to store the log tables in a separate MySQL database.

* The log tables have some extra columns `log_user_id`, `log_conn_id`, `log_action`, to describe which user performed the action, the MySQL connection ID, and the action that was taken.

* The log tables will begin by storing the initial values of your CiviCRM tables. Then each time a row in a CiviCRM table is added, deleted, or changed, the logging system will add a new row the appropriate log table to fully describe the change.

* The logging system adds 3 triggers on every `civicrm_` table to handle updating records, inserting records, and deleting records.

* Logging will _not_ destroy or change your existing data.

### Log_conn_id

The pre 4.7.7 log table format creates an integer field 13 chars long for the log_conn_id. The value stored here is the mysql CONNECTION_ID(). Unfortunately this mysql value is not actually unique. It is only unique at a given point in time. If you want to retrieve the changes made in a specific mysql transaction you need to add some time parameters - but unfortunately there is no accurate way to calculate what those time parameters should be since the transaction could be a batch transaction lasting 10 minutes or only 10 seconds. For anyone who has loaded change reports and found data missing the miscalculation of this timeframe is a likely cause. This was then changed to a 13 character varchar with using the `uniqid()` php function to generate the id

## Performance

In some testing there hasn't been any indication of any significant performance degredation to the system, however if your working on a large dataset there maybe be a need to do some performance testing with logging turned on before releasing it into production. In testing it showed that there wasn't any great difference in perfomance in INNODB v Archive engine storage.

## System requirements

* **MySQL Storage Engines** - By Default CiviCRM uses the Archive storage engine to store the logging data. The Archive engine may not automatically be installed on your MySQL system. Depending on your installation you maybe able to [install the Archive Engine](https://stackoverflow.com/questions/26996618/install-mariadb-archive-engine). However if you cannot install the Archive engine, the best solution would be to look to install the [nz.co.fuzion.innodbtriggers](https://github.com/eileenmcnaughton/nz.co.fuzion.innodbtriggers) Extension.

* **MySQL database permissions** - Logging requires the usage of [MySQL Triggers](https://dev.mysql.com/doc/en/triggers.html), so your MySQL database user will need to have the correct permissions to use them. In most cases, you will need to grant the [Trigger Privilege](https://dev.mysql.com/doc/en/privileges-provided.html#priv_trigger). However, depending on your system or if you have enabled binary logging, you may need the [Super Privilege](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super).

!!! note
    Previously, logging was not compatible with multilingual installations. As of 4.7, it should be compatible, but it's still experimental.


## Turning on logging

1. Make a complete backup of your database, just in case anything goes wrong.

1. If you wish to store your log tables in a separate MySQL database, then:

    1. Create the new database.

    1. Edit your `civicrm.settings.php` file to add information for the new logging database there, under `CIVICRM_LOGGING_DSN`. If you create a new database called `mydatabase_civicrm_logging` the line in `civicrm.settings.php` might look similar to this:

        ```php
        define(
           'CIVICRM_LOGGING_DSN' ,
           'mysql://user:pass@localhost/db_name?new_link=true'
        );
        ```
        
1. Go to **Administer › System Settings - Misc (Undelete, PDFs, Limits, Logging, Captcha, etc.)**

1. Set Logging to **Yes**.


## Turning off logging

1. Go to **Administer › System Settings - Misc (Undelete, PDFs, Limits, Logging, Captcha, etc.)**

1. Set Logging to **No**.

If you want to turn on logging again, after it's been off for some time, you can do so without losing any of the previous log data. New log data will simply be appended to the log tables.


## Inspecting small changes

1. View a contact record that has been altered after logging was enabled.

1. Go to the **Change Log** tab to view a table of changes.

1. For one update, click **Update** to view the fields that were updated.

## Swapping over to INNODB for storage format

While INNODB is a better option for most sites and the changed `log_conn_id` methodology is better for all it wasn't a change I wanted to put in the upgrade script, for the simple reason it could take a while. Converting and altering all the log tables on a large site is likely to require a planned outage - so instead of imposing that change we settled for

1. new sites turning on logging would use the new `log_conn_id` format

2. new sites turning on logging could get the INNODB table format and indexes by implementing a hook. The hook is implemented in this [extension](https://github.com/eileenmcnaughton/nz.co.fuzion.innodbtriggers/blob/master/innodbtriggers.php) and sites could opt to install that extension or add a similar hook before turning on logging if they choose

3. existing sites can migrate to the new `log_conn_id` by running an api function (probably by drush or similar) ```php civicrm_api3('System', 'updatelogtables', array())```;

4. existing sites can do a one way conversion to INNODB format by adding a hook per above prior to running that command. 

5. sites that wish to revert to ARCHIVE would need to do that through mysql - there is no process for this & it seems unlikely to be a good idea.

## Reporting on multiple changes

### Reports in CiviCRM Core

1. Go to **Administer > CiviReport > Create New Report From Template**.

1. With logging turned on, two new report templates are available: 
    * Contact Logging Report (Summary)
    * Contact Logging Report (Detail)

1. Use these report templates to create reports, as needed. See [CiviReport](https://docs.civicrm.org/user/en/latest/reporting/what-is-civireport/) for more info.

### Extensions

* The [Extended Logging Report](http://civicrm.org/extensions/extended-logging-report) offers some additional features over the built in reports which are designed to find out about individual transactions and don't cope with Batch or long running transactions. The extended logging report allows you to view all email address changes made this month (for example) or to see the changes made by an import job.

## References

There is a good write up on how logging is enabled the purpose in this [blog post](https://civicrm.org/blog/eileen/who-did-what-when).
