# Choosing your CMS

CiviCRM works with several popular open source Content Management Systems (CMS).

* Backdrop CMS
* Drupal 7
* Drupal 8
* Joomla
* WordPress

Before starting a project you should consider how you plan to use CiviCRM both now and in the future. Two big questions are:

1. How much information from CiviCRM do we want to display on our website (e.g. A live list of our current members)?
2. What information, collected through our website, do we want to store in CiviCRM (e.g. Family data from a public form with the family relationships automatically added).

This is known as integration. How much integration you require will help you determine which is the best CMS for you.

## Availability of Extensions

There are historical reasons why different CMS options have different levels of integration with CiviCRM. CiviCRM version 1.x did not originally support its own extension mechanism - the only option was to write an integration in the form of a Drupal module. Consequently, CiviCRM Drupal integrations grew first and fastest. In CiviCRM version 4.2 a flexible, cross-CMS extension system was introduced to CiviCRM further reducing disparity between the CMS options.

You may also choose a CMS because you already know how to use it or it is already used by your organisation. Each CMS has different advantages and disadvantages and different people will give you views on which is better. This chapter is aimed at giving you the requisite information to choose the best CMS for you.

## Integration Features

Broadly there are some differences which arise because of distinctive features/norms in each CMS community, e.g.

* In WordPress, it's pretty common to embed content in your web-pages using short-codes. CiviCRM has several short-codes in WP.
* In Backdrop CMS/Drupal 7/Drupal 8, it's pretty common to embed content in your web-pages using blocks. CiviCRM has several blocks in Drupal.
* In Backdrop CMS/Drupal 7/Drupal 8, CiviCRM makes use of the CMS's "roles" and "permissions" using the CMS's interfaces and approaches, and there are modules to synchronize CiviCRM groups and membership types to them.
* In Joomla/WordPress, "roles" and "permissions" can also be managed using the CMS's interfaces and approaches, and there are plugins to synchronize groups and membership types to them.

## Installation Process

The installers are a bit different - web-based installation is easier in Joomla, but it's more flexible in Drupal or WordPress. Command line based installation is easier in Drupal or WordPress.

## Testing

The core system is the same across all CMS options, but occasionally there are bugs in the CMS integration code. For historical reasons, the most robust automated testing is performed on Drupal7 though as of 2019 there are very active WordPress manual testers and a growing numbers of WordPress tests are being added. 

The size of the install base shown at http://stats.civicrm.org/?tab=sites also affects how well reported issues are and how quickly fixes are tested and merged. While trends are subject to change, as of 2019, Joomla shows the least growth and WordPress the most.

Therefore it may take a bit longer to identify/resolve bugs in the BackDrop, Drupal8, Joomla and WordPress integrations but automated testing on these platforms is being continually improved.

The following chapters in this section go into more detail about what is offered for each CMS.

## Users and Contacts

Anonymous site visitors and those with a website login are website **Users**.
Individuals and organisations with a CiviCRM record are **Contacts**.
