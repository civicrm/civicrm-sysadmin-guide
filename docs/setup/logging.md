# Detailed Logging

Detailed logging (aka "logging") allows you to maintain a full history of *all* changes made to *everything* in CiviCRM. It's a powerful feature which is turned *off* be default &mdash; because it's a bit overkill for most organizations, and it comes with some downsides too.

![Screenshot of report showing field differences within one update](../img/detailed-logging-report.png)


## How logging works

* Logging adds many new tables to your database &mdash; one new table for most existing CiviCRM tables in the database, each with a `log_` prefix (cache tables are ignored).

* Optionally (and recommended), you can choose to store the log tables in a separate MySQL database.

* The log tables have some extra columns `log_user_id`, `log_conn_id`, `log_action`, to describe which user performed the action, the MySQL connection ID, and the action that was taken.

    !!! note "Further Information"
        More information on changes to what is stored in the `log_conn_id` column can be found in the associated [blog](https://civicrm.org/blog/eileen/who-did-what-when) on this subject

* The log tables will begin by storing the initial values of your CiviCRM tables. Then each time a row in a CiviCRM table is added, deleted, or changed, the logging system will add a new row the appropriate log table to fully describe the change.

* The logging system adds 3 triggers on every `civicrm_` table to handle updating records, inserting records, and deleting records.

* Logging will _not_ destroy or change your existing data.

## Performance

In some testing there hasn't been any indication of any significant performance degradation to the system, however if your working on a large dataset there maybe be a need to do some performance testing with logging turned on before releasing it into production. In testing it showed that there wasn't any great difference in performance in INNODB v Archive engine storage.

## System requirements

* **MySQL Storage Engines** - Starting with version 5.16, CiviCRM uses the InnoDB storage engine to store logging data. Prior to 5.16, the Archive engine was used. Existing installations may continue to use the Archive engine, but migration to InnoDB is recommended for performance and relability reasons. Existing systems can be migrated using the `System.updatelogtables` API with the `forceEngineMigration` parameter set.

!!! warning
    Migrating existing log tables may take a long time especially on larger installations. Disk usage may also increase significantly when switching from Archive to InnoDB as data is not compressed. Refer to [Advanced Options](#advanced-options) for solutions that enable compression.

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

While INNODB is a better option for most sites and the changed `log_conn_id` methodology is better for all it wasn't a change that was implemented in the upgrade script, for the simple reason it could take a while. Converting and altering all the log tables on a large site is likely to require a planned outage - so instead of imposing that change we settled for

To enable InnoDB table format, install the [InnoDB Triggers extension](https://github.com/eileenmcnaughton/nz.co.fuzion.innodbtriggers) before enabling logging. To convert an existing site where logging is already enabled, install the extension, then run the ```civicrm_api3('System', 'updatelogtables', array());``` API command.

Sites that wish to revert to ARCHIVE would need to do that through mysql - there is no process for this & it seems unlikely to be a good idea.

!!! warning "Disk Space Usage"
    It should be noted that switching to INNODB is likely to increase the storage required by your MySQL database.

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

## Advanced Options

In its default configuration, detailed logging uses no compression for log tables and does not create indexes on any columns. For larger sites, this could cause log tables to use large amounts of disk space and might lead to performance issues for change log reports.

CiviCRM allows extensions developers to modify the schema definition of log tables, making it possible to solve some of these problems:

* The [nz.co.fuzion.innodbtriggers](https://github.com/eileenmcnaughton/nz.co.fuzion.innodbtriggers) extension can be used to enable InnoDB table compression as well as to create indexes on a number of columns that improve performance of built-in change log reports.
* [at.greenpeace.advancedlogtables](https://github.com/greenpeace-cee/at.greenpeace.advancedlogtables) works like nz.co.fuzion.innodbtriggers and ships with the same defaults, but allows dynamic configuration of table engine, engine configuration and indexes via settings. This extension can be used to restore the storage engine to Archive (the previous default which had some performance and durability issues) or to use more specialized engines like TokuDB.
