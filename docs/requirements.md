# Installation Requirements

Ensure that your system meets the following requirements before installing or upgrading CiviCRM.

If you are curious what technologies other organizations are using to run CiviCRM, you can look at the [CiviCRM Stats](https://stats.civicrm.org/?tab=technology).

## CMS {:#cms}

A CMS, or Content Management System, is a type of application which controls the manges the content of a website. CiviCRM must be installed within one of these compatible CMS platforms.

!!! tip
    See [choosing a CMS](/planning/cms.md) for more information about the advantages and disadvantages of each compatible CMS platform.

### Drupal

* Drupal 8 - Not yet supported

    !!! bug "Work in progress"
        CiviCRM functions with Drupal 8 but (as of October 2017) it has some unresolved issues, in particular [CRM-17652](https://issues.civicrm.org/jira/browse/CRM-17652). 
 
* Drupal 7 - Compatible with CiviCRM 4.1 and higher.

* Drupal 6 - No longer compatible (as of CiviCRM 4.3). It *might* work, but it's not supported. 

### WordPress

WordPress 3.4.x or newer is required. 

### Joomla

Joomla 2.5.x or 3.x.x is required.

### Backdrop 

Backdrop 1.0 or newer is required.

## PHP {:#php}

### PHP version

* PHP 7.2 - not yet tested
* PHP 7.1 - compatible with CiviCRM 4.7.24 and later, but not recommended due to insufficient testing
* PHP 7.0 - compatible and **recommended**
* PHP 5.6 - compatible and **recommended**
* PHP 5.5 - compatible, but not recommended due to to [PHP end-of life](http://php.net/eol.php) in July 2016
* PHP 5.4 - [deprecated for CiviCRM 4.7 as of March, 2018](https://civicrm.org/blog/totten/end-of-zombies-php-53-and-54). Also not recommended due to to [PHP end-of life](http://php.net/eol.php) in Sept 2015
* PHP 5.3 - [deprecated for CiviCRM 4.7 as of December, 2017](https://civicrm.org/blog/totten/end-of-zombies-php-53-and-54). Also not recommended due to to [PHP end-of life](http://php.net/eol.php) in August 2014

### PHP extensions

To install these extensions, you will typically install a separate package within your server's package manager (e.g. `apt-get` on Ubuntu).

* [PHP Curl](http://www.php.net/curl) - required for many payment processors, the extension manager, and the CiviCRM News dashlet
* [PHP SOAP](http://www.php.net/soap) - required to use the SOAP processor (In turn needed for the popular CiviSMTP service)
* [PHP DOM XML](http://www.php.net/manual/en/dom.setup.php) - required by CiviCase

### PHP configuration

* Set `memory_limit` between 256 and 512 megabytes
* Do NOT enable the deprecated `open_basedir` or `safemode` PHP directives. Otherwise you will have an error when automatically installing most of the extensions.


## MySQL

### MySQL version

* MySQL 5.1.x or higher is required

### MySQL configuration

* Support for the `innodb` storage engine is required
* The `thread_stack` configuration variable should be set to 192k or higher
* Trigger support is required
* SUPER privileges are required in MySQL 5.1 if binary logging is enabled

### MySQL alternatives

[MariaDB](https://mariadb.org/) and [Percona](https://www.percona.com/software/mysql-database/percona-server) are forks of the MySQL project and can mostly be used as drop-in replacements for MySQL. 

Other database servers such as Postgres are not compatible with CiviCRM.


## Server operating system {:#os}

If your server meets all of the previously-described requirements, CiviCRM *should* function correctly. There are no explicit operating system requirements. However, it's worth noting that CiviCRM is far more widely deployed and tested on UNIX-based operating systems, in particular Linux system (e.g. Ubuntu, Debian, etc.), than it is with Microsoft Windows. Nothing in theory should prevent you from running CiviCRM on a Windows server. But in practice, with a Windows server, you will be more likely to run into problems and less likely to find assistance from other CiviCRM community members.

## Server hosting

In general, CiviCRM is a demanding web application which requires substantial server resources. It may not perform well on all hosting platforms. Learn more about [choosing your hosting platform](/planning/hosting.md).

# CiviCRM PHP Requirements

* Requirements
* Checking Requirements

* Drupal

## Requirements

PHP version:

* PHP 7.1 As of CiviCRM 4.7.24 (due to be released at the start of September 2017) is in experimental support we recommend using 7.0 for now. (test suite passes but no community usage as yet).
* PHP 7.0 is compatible with CiviCRM 4.7.12 onwards. It is not compatible with 4.6 (but there is some discussion of adding this). (Deployed by over 500 sites as at July 2017, and test suites pass.)
* PHP 5.6+ is compatible. (This has the widest testing and userbase as well as forward-compatibility.)
* PHP 5.3, 5.4, 5.5 are deprecated. (These may still work OK today; In the 4.7 versions support for 5.3 will end at 2017-12-31 and support for 5.4 will end at 2018-02-28 See this [notice](https://civicrm.org/blog/totten/end-of-zombies-php-53-and-54) for more information. Support for 5.5 will remain but expect to see it dropped in future CiviCRM releases. In 4.6 there are no plans to change the minimum supported level which is PHP5.3)
* We recommend your version of PHP is supported (and patched) for security fixes. [Security support for PHP5.5 ended mid 2016, PHP5.6 ends 2018](http://php.net/supported-versions.php).
* CiviCRM Stats Project shows a breakdown of "in the wild" usage at [https://stats.civicrm.org/?tab=technology](https://stats.civicrm.org/?tab=technology) - this may guide decisions.

Extensions:

* The[PHP Curl Extension](http://www.php.net/curl)is required for many Payment Processors, for the extension manager and for the CiviCRM News Dashlet
    * For Ubuntu: sudo apt-get install php5-curl
* The [PHP SOAP Extension](http://www.php.net/soap) is required to use the SOAP processor (In turn needed for the popular CiviSMTP service)
* CiviCase requires the [PHP DOM xml Extension](http://www.php.net/manual/en/dom.setup.php)

Configuration:

* Do NOT enable the deprecated open_basedir or safemode php directives (eg. you will have an error installing automatically most of the extensions).

## Checking Requirements

You can use the phpinfo() function to get information about your PHP version and enabled PHP extensions.

### Drupal

You can use the Reports >> Status Report page then choose "more info" beside the PHP entry to see the phpinfo() output.# CiviCRM MySQL Permission Requirements

CiviCRM requires MySQL (or equivalent, such as MariaDB) version 5.1+ therefore the first release that is compatible with CiviCRM is 5.1.3 - see Wikipedia for [MySQL version history](http://en.wikipedia.org/wiki/MySQL#Versions).

The permissions you assign to the CiviCRM MySQL account will **depend on your version of MySQL**.

If your version of MySQL is **greater than or equal to** 5.7.6 you will need the following for all typical uses of CiviCRM 4.3+ including installing extensions:

Also, to enable CiviCase you should set CREATE VIEW permission.

```
-- Assuming a database called "civicrm" and a user called "civicrm_user"
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, TRIGGER, CREATE ROUTINE, ALTER ROUTINE, REFERENCES ON civicrm.* TO 'civicrm_user'@'localhost' IDENTIFIED BY 'realpasswordhere';
```

If your version of MySQL is **greater than** 5.1.6 but **less than** 5.7.6 you will need the following for all typical uses of CiviCRM 4.3+ including installing extensions:

**NOTE:** The extra **TRIGGER** permission.

Also, to enable CiviCase you should set CREATE VIEW permission.

```
-- Assuming a database called "civicrm" and a user called "civicrm_user"
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, TRIGGER, CREATE ROUTINE, ALTER ROUTINE ON civicrm.* TO 'civicrm_user'@'localhost' IDENTIFIED BY 'realpasswordhere';
```

If your version of MySQL is **less than** 5.1.6 you will need the following for all typical uses of CiviCRM 4.3+ including installing extensions:

```
-- Assuming a database called "civicrm" and a user called "civicrm_user"
GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, CREATE ROUTINE, ALTER ROUTINE ON civicrm.* TO 'civicrm_user'@'localhost' IDENTIFIED BY 'realpasswordhere';

GRANT SUPER ON *.* TO 'civicrm_user'@'localhost';
```

If you want to enable binary logging you will need to add the "log_bin_trust_function_creators = 1" in your MySQL configuration (/etc/my.cnf). See the [MySQL manual reference](http://dev.mysql.com/doc/refman/5.1/en/server-system-variables.html#sysvar_log_bin_trust_function_creators) for more information.

```
log_bin_trust_function_creators = 1
```

Alternatively, you can give the SUPER permission even if your version is greater than 5.1.6.

```
-- Assuming a database called "civicrm" and a user called "civicrm_user"
GRANT SUPER ON *.* TO 'civicrm_user'@'localhost';
```

More information on triggers is available in the MySQL documentation about [Binary Logging of Stored Programs](http://dev.mysql.com/doc/refman/5.1/en/stored-programs-logging.html). The MariaDB manual also has good documentation on [binary logging](https://mariadb.com/kb/en/binary-log/).# checkMysqlTime

CiviCRM performs various operations based on dates and times – for example, deactivating expired records or triggering scheduled reminders. To perform these operations correctly, the dates and times reported by PHP and MySQL should match. If the dates or times are mismatched, then one or more of the following may be the problem:

* PHP and MySQL may be running on different servers, and the clocks may be out-of-sync. Configuring automatic clock synchronization is the best solution.
* PHP, MySQL, and/or the operating system may be configured to use different default timezones. Verify the configuration of each.
* The content management system (Drupal, Joomla, or WordPress) or one of its plugins may manipulate the timezone settings without CiviCRM's awareness. You may wish to post to the [CiviCRM Forum](http://forum.civicrm.org/) about your problem. Please include any available details about the timezone settings in the operating system, PHP, MySQL, and the CMS; if you have any special CMS plugins or configuration options which may affect timezones, please report them.