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

