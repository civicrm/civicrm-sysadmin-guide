# Installation Requirements

Ensure that your system meets the following requirements before installing or upgrading CiviCRM.

If you are curious what technologies other organizations are using to run CiviCRM, you can look at the [CiviCRM Stats](https://stats.civicrm.org/?tab=technology).

## Server Environment {:#server}

### Operating System {:#os}

If your server meets all of the requirements described on this page, CiviCRM *should* function correctly. There are no explicit operating system requirements. However, it's worth noting that CiviCRM is far more widely deployed and tested on UNIX-based operating systems, in particular Linux (e.g. Ubuntu, Debian, etc.), than with Microsoft Windows. You can still use CiviCRM fine from Windows desktops when the hosting environment runs Linux.

### Hosting

In general, CiviCRM is a demanding web application which requires substantial server resources. It may not perform well on all hosting platforms. Learn more about [choosing your hosting platform](/planning/hosting.md).

## CMS {:#cms}

A CMS, or Content Management System, is a type of application which controls and manages the content of a website. CiviCRM must be installed within one of these compatible CMS platforms.

See our page on [choosing a CMS](/planning/cms.md) for more information about the advantages and disadvantages of each compatible CMS platform.

### Drupal

* Drupal 8 - Not yet officially supported

    !!! bug "Work in progress"
        CiviCRM functions with Drupal 8 but (as of October 2019) it has some minor bugs, and doesn't have an official release, relying instead on some unofficial tools and processes. See [the integration module](https://github.com/civicrm/civicrm-drupal-8/blob/master/README.md) for more information.

* Drupal 7 - Compatible with CiviCRM 4.1 and higher.

* Drupal 6 - No longer supported.

### WordPress

* WordPress 3.4.x or newer is required.

### Joomla

* Joomla 3.x.x is required.

### Backdrop

* Backdrop 1.0 or newer is required.

## PHP {:#php}

### PHP Version

|  | CiviCRM 5.13 ESR | CiviCRM 5.x.x stable |
| -- | -- | -- |
| PHP 7.3 |testing incomplete but preliminary testing did not find any issues|compatible and **recommended**|
| PHP 7.2 | compatible and **recommended** - but see note below about resaving the SMTP password| compatible and **recommended** but see note below about resaving the SMTP password|
| PHP 7.1 | compatible but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in Dec 2019 | compatible but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in Dec 2019 |
| PHP 7.0 | compatible but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in Dec 2018   | compatible but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in Dec 2018   |
| PHP 5.6 | compatible but **not recommended** due to to [PHP end-of life](http://php.net/eol.php) in Dec 2018   | incompatible as of 5.15.1 |
| PHP 5.5 | **incompatible** | **incompatible** |
| PHP 5.4 | **incompatible** | **incompatible** |
| PHP 5.3 | **incompatible**  |  **incompatible** |

### PHP Extensions

To install these extensions, you will typically install a separate package within your server's package manager (e.g. `apt-get` on Ubuntu).

#### Required for CiviCRM Core

* [PHP BCMath](https://www.php.net/bcmath) - required for calculating financial values in CiviCRM Core.
* [PHP Curl](http://www.php.net/curl) - required for many payment processors, the extension manager, and the CiviCRM News dashlet.
* [PHP DOM XML](http://www.php.net/manual/en/dom.setup.php) - required by CiviCase.
* [PHP Multibyte](http://php.net/manual/en/ref.mbstring.php) - required for internationalisation and proper encoding of fields.
* [PHP Zip](http://php.net/manual/en/book.zip.php) - required for unzipping auto-downloaded extensions so they can be installed from the browser.

#### Required for Third-Party Functionality or CiviCRM Extensions

* [PHP SOAP](http://www.php.net/soap) - required to use the SOAP processor (required for the CiviSMTP service)

#### Historical Requirements - See Notes

* [PHP MCrypt](http://php.net/manual/en/intro.mcrypt.php) - the MCrypt extension is no longer recommended for new installations.

    !!! warning "PHP 7.2 Compatibility"
        7.2 upgrade warning - 7.2 does not support mcrypt and if mcrypt is not installed the smtp password (if entered) will need to be re-saved once you update your php version to 7.2.

### PHP Configuration

* Set `memory_limit` between 256 and 512 megabytes
* Don't enable the deprecated `open_basedir` or `safemode` PHP directives. Otherwise you will have an error when automatically installing most of the extensions.
* Don't use MAMP XCache - Several people have reported "white screen of death" trying to run CiviCRM with MAMP's XCache enabled (check MAMP > Preferences > PHP > Cache).

## MySQL

CiviCRM requires MySQL (or compatible) database software.

[MariaDB](https://mariadb.org/) and [Percona](https://www.percona.com/software/mysql-database/percona-server) are forks of the MySQL project and can be used as drop-in replacements for MySQL.

Other database servers (such as PostgreSQL) are not compatible with CiviCRM.

### MySQL Version

Your MySQL version should be **5.7.5 or greater** or MariaDB **10.0.2 or greater**.  CiviCRM does still work on older versions but there is some functionality missing, notably around ensuring various database actions don't compete for the same resources.

#### MySQL 8

CiviCRM is currently not known to work with MySQL 8, there is an open issue for [MySQL 8 support](https://lab.civicrm.org/dev/core/issues/392). It is also worth knowing that both [Backdrop](https://forum.backdropcms.org/forum/installing-backdrop-1126-mysql-8-sqlmode-cant-be-set-value-noautocreateuser) and [Drupal 7](https://www.drupal.org/project/drupal/issues/2978575) with open issues in regards to MySQL 8 support. [Drupal 8](https://www.drupal.org/docs/8/system-requirements/database-server) as of 8.6 as per this [issue](https://www.drupal.org/project/drupal/issues/2966523), WordPress and [Joomla](https://docs.joomla.org/Joomla_and_MySQL_8) appears to be compatible with MySQL 8.

### MySQL Configuration

* Support for the `innodb` storage engine is required.
* The `thread_stack` configuration variable should be set to 192k or higher.
* Trigger support is required.
* The `ONLY_FULL_GROUP_BY` mode should be turned off on production sites - although this is mostly precautionary now.
    * In MySQL 5.7+ the SQL mode `ONLY_FULL_GROUP_BY` is enabled by default which causes some errors due to some of CiviCRM's SQL queries that were written with the assumption that this mode would be disabled.
    * Administrators are advised to turn off this SQL mode by removing the string `ONLY_FULL_GROUP_BY` from the `sql_mode` variable. The following SQL query will do the trick.
        ```sql
        SET GLOBAL sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
        ```
    * See also:
        * A popular [Stack Exchange question](https://stackoverflow.com/a/36033983/895563) discussing this issue
        * [MySQL documentation on `sql_mode`](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sql-mode-setting)
* If you plan to have multiple lanaguages used in your CiviCRM installation it is strongly recommended that you ensure that `locale` is set to a UTF8 locale and also ensure that you set utf8 as the standard encoding for MySQL. To alter locale you can configure as per [Ubuntu instructions](https://www.thomas-krenn.com/en/wiki/Configure_Locales_in_Ubuntu), [Debian](https://wiki.debian.org/Locale), [CentOS](https://www.rosehosting.com/blog/how-to-set-up-system-locale-on-centos-7/). To set the default encoding for MySQL you should follow the steps provided in this [Stack Overflow post](https://stackoverflow.com/questions/3513773/change-mysql-default-character-set-to-utf-8-in-my-cnf)
* In order to support a future data migration from `utf8` to the `utf8mb4` character set, it is recommended, though not yet required, to add the following configuration directives on MySQL 5.5 or 5.6 (these are the default values on MySQL 5.7):
    ```
    [mysqld]
    innodb_large_prefix=true
    innodb_file_format=barracuda
    innodb_file_per_table=true
    ```
* In MySQL 8 the default authentication plugin changes from `mysql_native_password` to `caching_sha2_password`. This may cause issues with your PHP layer. Fortunately you can revert this back by specifying in your MySQL configuration as follows:
  ```
   [mysqld]
   default_authentication_plugin=mysql_native_password
  ```
  Also in MySQL 8 importantly the following variables have been removed `innodb_large_prefix`, `innodb_file_format`. In MySQL 8 Binary logging is also turned on by default which many users may want to turn off, this can be done by adding to your MySQL configuration file
  ```
   [mysqld]
   skip-log-bin
  ```

#### MySQL Time

CiviCRM performs various operations based on dates and times – for example, deactivating expired records or triggering scheduled reminders. To perform these operations correctly, the dates and times reported by PHP and MySQL should match. If the dates or times are mismatched, then one or more of the following may be the problem:

* PHP and MySQL may be running on different servers, and the clocks may be out-of-sync. Configuring automatic clock synchronization is the best solution.
* PHP, MySQL, and/or the operating system may be configured to use different default timezones. Verify the configuration of each.
* The content management system (Drupal, Joomla, or WordPress) or one of its plugins may manipulate the timezone settings without informing CiviCRM's. You may wish to post to [Stack Exchange](https://civicrm.stackexchange.com/) or [Mattermost](https://chat.civicrm.org) about your problem. Please include any available details about the timezone settings in the operating system, PHP, MySQL, and the CMS; if you have any special CMS plugins or configuration options which may affect timezones, please report them.


### MySQL Permissions

The permissions you'll need to assign to the MySQL user that CiviCRM uses will **depend on your version of MySQL**. The following example assumes you have a database called `civicrm` and a MySQL user called `civicrm_user`.

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
  REFERENCES,
  CREATE VIEW,
  SHOW VIEW
ON civicrm.*
TO 'civicrm_user'@'localhost'
IDENTIFIED BY 'realpasswordhere';
```

##### Binary Logging

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
