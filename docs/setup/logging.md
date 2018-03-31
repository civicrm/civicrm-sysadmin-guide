# Detailed Logging Setting

In CiviCRM there is a logging setting that allows for "detailed" logging to be turned on. Detailed logging tracks changes to the main `civicrm_` tables by creating duplicate tables called `log_civicrm_x`. A few extra columns are added to the `log_x` table `log_user_id`, `log_conn_id`, `log_action`, these describe which user did the action, the MySQL connection ID and the action that was taken. It is important to not that turning logging on and off will not destroy any data. It is recommended tho that a separate database is created to store the log tables.

Once you have added the database to your MySQL system you will need to alter your `civicrm.settings.php` to add information for the new logging database there, under `CIVICRM_LOGGING_DSN`. If you create a new database called `mydatabase_civicrm_logging` the line in `civicrm.settings.php` might look similar to this:

```php
  define( 'CIVICRM_LOGGING_DSN' , 'mysql://sqlusername:sqlpassword@localhost/mydatabase_civicrm_logging?new_link=true' );
```

Before creating this new database for the logging tables or turning logging on in the admin menu, be sure to make a complete backup copy of your existing database. When detailed logging is turned on 3 triggers are created on every `civicrm_x` table to handle the following: Inserting new records, Updating existing records, deleting records. When logging is first turned on it replicates all the data into the log tables with the action `initalization`.


## MySQL Storage and Permissions.

### Permissions

The Detailed logging requires the usage of Triggers in MySQL. If your database user does not have the propoer authority you may neeed to create a new user or add the correct permissions to your CiviCRM MySQL user. In most cases this will mean you will need the [Trigger Privilege](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_trigger). However depending on your system or if you have enabled binary logging you may need the [Super Privilege](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super).

### Storage Engine

By Default CiviCRM uses the Archive storage engine to store the detailed logging data. The Archive engine may not automatically be installed on your MySQL system. Depending on your installation you maybe able to [install the Archive Engine](https://stackoverflow.com/questions/26996618/install-mariadb-archive-engine). However if you cannot install the Archive engine, the best solution would be to look to install the [nz.co.fuzion.innodbtriggers](https://github.com/eileenmcnaughton/nz.co.fuzion.innodbtriggers) Extension.

## Reports

When detailed logging is turned on 2 report templates added `CRM_Report_Form_Contact_LoggingSummary` and `CRM_Report_Form_Contact_LoggingDetail` You can Create reports based on these templates by going to Reports -> Create Report from Template. The reports allow you to view the changes that are made to contacts. These reports may not be perfectly built and as of CiviCRM 4.2.2 were treated as being in Beta. You can download an Extension with extended reports for logging that provides basic functionality for contact changes and takes the place of the two contact reports mentioned above. Download the extension here: [http://civicrm.org/extensions/extended-logging-report/version-10-0](http://civicrm.org/extensions/extended-logging-report/version-10-0) and install by following the instructions here: [Extensions](/customize/extensions.md). Once installed, create report templates at Administer/CiviReport/Manage Templates with these values:

* Extended Logging Summary
  * URL: contact/extendedloggingsummary
  * Class: `CRM_Extendedloggingreports_Form_Report_CRM_extendedloggingreports_Report_Form_Contact_LoggingSummary`
* Extended Logging Detailed
  * URL: contact/extendedloggingdetail
  * `Class: CRM_Extendedloggingreports_Form_Report_CRM_extendedloggingreports_Report_Form_Contact_LoggingDetail`

Then go to Administer/CiviReport/Create Reports from Templates and create a report from the Summary template. You won't need to create a report from the Detail template because it is access from the Summary template. Using the URLs above when you create the templates is the key to getting the summary report to link to the detail report successfully.

## Multilingual Databases

As of 4.7 it appears the detailed logging system can cope with Multilingual databases. However this support is still experimental. There have been a few tests added to the CiviCRM's unit tests to prove that the detailed logging system can cope with multilingual databases.

## Further Information

A rundown of how the logging system works is in this article: [http://civicrm.org/blogs/shot/new-civicrm-33-feature-logging](http://civicrm.org/blogs/shot/new-civicrm-33-feature-logging)

