# Installation Requirements

Ensure that your system meets the following requirements before installing or upgrading CiviCRM.

If you are curious what technologies other organizations are using to run CiviCRM, you can look at the [CiviCRM Stats](https://stats.civicrm.org/?tab=technology).

## Server environment {:#server}

### Operating system {:#os}

If your server meets all of the requirements described on this page, CiviCRM *should* function correctly. There are no explicit operating system requirements. However, it's worth noting that CiviCRM is far more widely deployed and tested on UNIX-based operating systems, in particular Linux system (e.g. Ubuntu, Debian, etc.), than it is with Microsoft Windows. Nothing in theory should prevent you from running CiviCRM on a Windows server. But in practice, with a Windows server, you will be more likely to run into problems and less likely to find assistance from other CiviCRM community members.

### Hosting

In general, CiviCRM is a demanding web application which requires substantial server resources. It may not perform well on all hosting platforms. Learn more about [choosing your hosting platform](/planning/hosting.md).

## CMS {:#cms}

A CMS, or Content Management System, is a type of application which controls the manges the content of a website. CiviCRM must be installed within one of these compatible CMS platforms.

See our page on [choosing a CMS](/planning/cms.md) for more information about the advantages and disadvantages of each compatible CMS platform.

### Drupal

* Drupal 8 - Not yet supported

    !!! bug "Work in progress"
        CiviCRM functions with Drupal 8 but (as of October 2017) it has some unresolved issues, in particular [CRM-17652](https://issues.civicrm.org/jira/browse/CRM-17652). 
 
* Drupal 7 - Compatible with CiviCRM 4.1 and higher.

* Drupal 6 - No longer compatible (as of CiviCRM 4.3). It *might* work, but it's not supported. 

### WordPress

* WordPress 3.4.x or newer is required. 

### Joomla

* Joomla 3.x.x is required.

### Backdrop 

* Backdrop 1.0 or newer is required.

## PHP {:#php}

### PHP version

|  | CiviCRM 4.6.x | CiviCRM 5.x.x | 
| -- | -- | -- |
| PHP 7.2 | **incompatible** | compatible and **recommended** with the proviso below*|
| PHP 7.1 | **incompatible** | compatible and **recommended** |
| PHP 7.0 | **incompatible** | compatible |
| PHP 5.6 | compatible and **recommended** | compatible |
| PHP 5.5 | compatible, but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in July 2016  |compatible until end of 2018, but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in July 2016 |
| PHP 5.4 | compatible but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in Sept 2015 | **incompatible** |
| PHP 5.3 | compatible but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in August 2014 |  **incompatible** |

* 7.2 proviso - 7.2 does not support mcrypt and if mcrypt is not installed the smtp password (if entered) will not be encrypted in the database.

### PHP extensions

To install these extensions, you will typically install a separate package within your server's package manager (e.g. `apt-get` on Ubuntu).

* [PHP Curl](http://www.php.net/curl) - required for many payment processors, the extension manager, and the CiviCRM News dashlet
* [PHP SOAP](http://www.php.net/soap) - required to use the SOAP processor (In turn needed for the popular CiviSMTP service)
* [PHP DOM XML](http://www.php.net/manual/en/dom.setup.php) - required by CiviCase
* [PHP Mcrypt](http://php.net/manual/en/intro.mcrypt.php) - Required for SMTP credentials and other credentials in the database. If you add Mcrypt on after running CiviCRM for a while you will need to re-save your passwords to the database.
* [PHP Multibyte](http://php.net/manual/en/ref.mbstring.php) - Required for internationalisation and proper encoding of fields.

### PHP configuration

* Set `memory_limit` between 256 and 512 megabytes
* Don't enable the deprecated `open_basedir` or `safemode` PHP directives. Otherwise you will have an error when automatically installing most of the extensions.
* Don't use MAMP XCache - Several people have reported "white screen of death" trying to run CiviCRM with MAMP's XCache enabled (check MAMP > Preferences > PHP > Cache).
* Don't use [eAccelerator](http://eaccelerator.net) - Several people have reported problems with this performance optimiser such as "white screen of death" and "snippet type is unrecognised".

## MySQL

CiviCRM requires MySQL (or compatible) database software.

[MariaDB](https://mariadb.org/) and [Percona](https://www.percona.com/software/mysql-database/percona-server) are forks of the MySQL project and can mostly be used as drop-in replacements for MySQL. 

Other database servers such as Postgres are not compatible with CiviCRM.

### MySQL version

Your MySQL version must be **5.1.3 or greater**.
 
### MySQL configuration

* Support for the `innodb` storage engine is required.
* The `thread_stack` configuration variable should be set to 192k or higher.
* Trigger support is required.
* The `ONLY_FULL_GROUP_BY` mode should be turned off.
    * In MySQL 5.7+ the SQL mode `ONLY_FULL_GROUP_BY` is enabled by default which causes some errors due to some of CiviCRM's SQL queries that were written with the assumption that this mode would be disabled.
    * Administrators are advised to turn off this SQL mode by removing the string `ONLY_FULL_GROUP_BY` from the `sql_mode` variable. The following SQL query will do the trick.
        ```sql
        SET GLOBAL sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
        ```
    * See also:
        * A popular [Stack Exchange question](https://stackoverflow.com/a/36033983/895563) discussing this issue
        * [MySQL documentation on `sql_mode`](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sql-mode-setting)

#### MySQL time

CiviCRM performs various operations based on dates and times – for example, deactivating expired records or triggering scheduled reminders. To perform these operations correctly, the dates and times reported by PHP and MySQL should match. If the dates or times are mismatched, then one or more of the following may be the problem:

* PHP and MySQL may be running on different servers, and the clocks may be out-of-sync. Configuring automatic clock synchronization is the best solution.
* PHP, MySQL, and/or the operating system may be configured to use different default timezones. Verify the configuration of each.
* The content management system (Drupal, Joomla, or WordPress) or one of its plugins may manipulate the timezone settings without CiviCRM's awareness. You may wish to post to [Stack Exchange](https://civicrm.stackexchange.com/) or [Mattermost](https://chat.civicrm.org) about your problem. Please include any available details about the timezone settings in the operating system, PHP, MySQL, and the CMS; if you have any special CMS plugins or configuration options which may affect timezones, please report them.


### MySQL permissions

The permissions you'll need to assign to the MySQL user that CiviCRM uses will **depend on your version of MySQL**. The following examples assume you have a database called `civicrm` and a MySQL user called `civicrm_user`.

#### MySQL 5.7.6+

```sql
GRANT
  SELECT,
  INSERT,
  UPDATE,
  DELETE,
  CREATE,
  DROP,
  INDEX,
  ALTER,
  CREATE TEMPORARY TABLES,
  LOCK TABLES,
  TRIGGER,
  CREATE ROUTINE,
  ALTER ROUTINE,
  REFERENCES 
ON civicrm.* 
TO 'civicrm_user'@'localhost' 
IDENTIFIED BY 'realpasswordhere';
```

#### MySQL 5.1.6 - 5.7.6

```sql
GRANT
  SELECT,
  INSERT,
  UPDATE,
  DELETE,
  CREATE,
  DROP,
  INDEX,
  ALTER,
  CREATE TEMPORARY TABLES,
  LOCK TABLES,
  TRIGGER,
  CREATE ROUTINE,
  ALTER ROUTINE
ON civicrm.*
TO 'civicrm_user'@'localhost'
IDENTIFIED BY 'realpasswordhere';
```

#### MySQL 5.1.6 and below

```sql
GRANT
  SELECT,
  INSERT,
  UPDATE,
  DELETE,
  CREATE,
  DROP,
  INDEX,
  ALTER,
  CREATE TEMPORARY TABLES,
  LOCK TABLES,
  CREATE ROUTINE,
  ALTER ROUTINE
ON civicrm.*
TO 'civicrm_user'@'localhost'
IDENTIFIED BY 'realpasswordhere';

GRANT SUPER ON *.* TO 'civicrm_user'@'localhost';
```

#### Special permissions

##### CiviCase

To use CiviCase you will also need the `CREATE VIEW` permission.

##### Binary logging

If you want to enable binary logging you will need to choose one of the following. Either:


* Add the following line to your `/etc/my.cnf` file.

    ```
    log_bin_trust_function_creators = 1
    ```
    
    (See the [MySQL manual reference](http://dev.mysql.com/doc/refman/5.1/en/server-system-variables.html#sysvar_log_bin_trust_function_creators) for more information.)

* Or give the `SUPER` permission (even if your MySQL version is greater than 5.1.6).

    ```sql
    GRANT SUPER ON *.* TO 'civicrm_user'@'localhost';
    ```
    
    (More information on triggers is available in the MySQL documentation about [Binary Logging of Stored Programs](http://dev.mysql.com/doc/refman/5.1/en/stored-programs-logging.html).) 

