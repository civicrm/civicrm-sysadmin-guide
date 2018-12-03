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

## ARCHIVE vs INNODB

The default storage engine for logging is ARCHIVE, which is optimised for fast writes and small size. That maybe what you want but note that consulting log tables is very slow with this storage engine. The [INNODB logging extension](https://github.com/eileenmcnaughton/nz.co.fuzion.innodbtriggers) swaps the table format for log tables from ARCHIVE to INNODB, speeding up querying the log tables at the expense of slightly slower writes and a larger log table.

## System requirements

* **MySQL Storage Engines** - By Default CiviCRM uses the Archive storage engine to store the logging data. The Archive engine may not automatically be installed on your MySQL system. Depending on your installation you maybe able to [install the Archive Engine](https://stackoverflow.com/questions/26996618/install-mariadb-archive-engine). If you cannot install the Archive engine, consider using [nz.co.fuzion.innodbtriggers](https://github.com/eileenmcnaughton/nz.co.fuzion.innodbtriggers) Extension.

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


## Reporting on multiple changes

### Reports in CiviCRM Core

1. Go to **Administer > CiviReport > Create New Report From Template**.

1. With logging turned on, two new report templates are available: 
    * Contact Logging Report (Summary)
    * Contact Logging Report (Detail)

1. Use these report templates to create reports, as needed. See [CiviReport](https://docs.civicrm.org/user/en/latest/reporting/what-is-civireport/) for more info.

### Extensions

* The [Extended Logging Report](http://civicrm.org/extensions/extended-logging-report) offers some additional features over the built in reports which are designed to find out about individual transactions and don't cope with Batch or long running transactions. The extended logging report allows you to view all email address changes made this month (for example) or to see the changes made by an import job.
