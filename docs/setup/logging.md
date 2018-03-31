# Logging Setting aka Detailed Logging 

This Documentation will cover the Logging Setting on the screen at **CiviCRM › Administer CiviCRM › Settings - Undelete, Logging and ReCAPTCHA**

If enabled, all actions performed on non-cache tables will be logged (in the respective log_* tables). A message indicates: _Logging functionality is currently in beta. Please test this feature on a copy of your database prior to using it on a production site._ More details about logging and how it works:

* Enabling logging will not destroy or change your existing data. However it will add many new tables to your database–one new table for most existing CiviCRM tables in the database, each with a `log_` prefix.
* In these tables the logging system will store the initial values of your current CiviCRM tables and then each time a row is added, deleted, or changed to your existing tables, the new row will be added to the `log_` table along with extra fields showing the time, user who made the change, and type of change.
* It is suggested to create a new database and then edit your `civicrm.settings.php` file to add information for the new logging database there, under `CIVICRM_LOGGING_DSN`. If you create a new database called `mydatabase_civicrm_logging` the line in `civicrm.settings.php` might look similar to this:

```php
define( 'CIVICRM_LOGGING_DSN' , 'mysql://sqlusername:sqlpassword@localhost/mydatabase_civicrm_logging?new_link=true' );

```

* Before creating this new database for the logging tables or turning logging on in the admin menu, be sure to make a complete backup copy of your existing database.

    !!! note "MySQL Storage Engines"
        By Default CiviCRM uses the Archive storage engine to store the detailed logging data. The Archive engine may not automatically be installed on your MySQL system. Depending on your installation you maybe able to [install the Archive Engine](https://stackoverflow.com/questions/26996618/install-mariadb-archive-engine). However if you cannot install the Archive engine, the best solution would be to look to install the [nz.co.fuzion.innodbtriggers](https://github.com/eileenmcnaughton/nz.co.fuzion.innodbtriggers) Extension.

    !!! warning "MySQL database permissions"
         When you enable logging, your civicrm database user must have authority to create triggers. If your database user does not have proper authority you will see a generic DB error message when enabling logging. You may need to create a new DB user with the necessary authority and use that DB account when running civicrm.
         
* A rundown of how the logging system works is in this article: [http://civicrm.org/blogs/shot/new-civicrm-33-feature-logging](http://civicrm.org/blogs/shot/new-civicrm-33-feature-logging)
* The reporting system is still definitely in beta in CiviCRM 4.2. In 4.2, the example contact report templates, mentioned in the article above, are not automatically created. However, two contribution logging reports are available–looking under Reports/Reports Listing.
* You can create the two contact logging reports, mentioned in the article above, in Administer/CiviReport/Manage Templates with these values for the Logging Summary Report:

        * URL: /contact/loggingsummary
        * Class: CRM_Report_Form_Contact_LoggingSummary

        And these values for the Detail Report:
        
        * URL: /contact/loggingdetail
        * Class: CRM_Report_Form_Contact_LoggingDetail

        * Then go to Administer/CiviReport/Create Reports from Templates and create the Contact Logging (summary) report. That report works but the links to the detail report may not. You may be able to get the detail report to work, by adding "&force=1" to the URL of the detail report. See [http://forum.civicrm.org/index.php?topic=18503.0](http://forum.civicrm.org/index.php?topic=18503.0) but These reports are definitely in beta quality as of CiviCRM 4.2.2.

    * You can download an Extension with extended reports for logging that provides basic functionality for contact changes and takes the place of the two contact reports mentioned above. Download the extension here: [http://civicrm.org/extensions/extended-logging-report/version-10-0](http://civicrm.org/extensions/extended-logging-report/version-10-0) and install by following the instructions here: [Extensions](/customize/extensions.md). Once installed, create report templates at Administer/CiviReport/Manage Templates with these values:

        * URL: contact/extendedloggingsummary
        * Class: CRM_Extendedloggingreports_Form_Report_CRM_extendedloggingreports_Report_Form_Contact_LoggingSummary

        * URL: contact/extendedloggingdetail
        * Class: CRM_Extendedloggingreports_Form_Report_CRM_extendedloggingreports_Report_Form_Contact_LoggingDetail

* Then go to Administer/CiviReport/Create Reports from Templates and create a report from the Summary template. You won't need to create a report from the Detail template because it is access from the Summary template. Using the URLs above when you create the templates is the key to getting the summary report to link to the detail report successfully.

