# Choosing your CMS

In general you should probably choose a CMS because you already know how to use it or it is already used by your organisation. Each CMS has different advantages and disadvantages and different people will give you views on which is better. This chapter is aimed at giving you the requisite information to choose the best CMS for you.

CiviCRM works with several popular open source Content Management Systems (CMS).

* Backdrop CMS
* Drupal 7
* Drupal 8
* Joomla
* WordPress
There are lots of resources that help guide organizations in their selection of the best CMS for their content strategy and website. There continue to be significant changes in the popularity of the CMSes over time given changes in the capabilities of the CMSes and the needs of the market, as seen by [https://w3techs.com/technologies/history_overview/content_management/ms/y](https://w3techs.com/technologies/history_overview/content_management/ms/y).

The choice of which CMS to use with CiviCRM is generally appropriately determined by factors other than differences in how CiviCRM integrates with each CMS. For example, your website may already be built using one of these CMS and there may be no need to go to the expense and hassle of reimplementing it in a different CMS. Or your CMS project may have good reasons for preferring one CMS over another. This page discusses how there may be some factors relating to your use of CiviCRM that may affect or be crucial to your decision on which CMS to use, even if many organizations find these points do not play a significant role.
Before starting a project you should consider how you plan to use CiviCRM both now and in the future. Two big questions are:

1. How much information from CiviCRM do we want to display on our website (e.g. A live list of our current members)?
2. What information, collected through our website, do we want to store in CiviCRM (e.g. Family data from a public form with the family relationships automatically added).

This is known as integration. The type of integration you require may help you determine which is the best CMS for you.

## Availability of Extensions

There are historical reasons why different CMS options have different levels of integration with CiviCRM. In 2005, CiviCRM launched without its own extension mechanism, but it was possible to write custom integrations in the form of Drupal modules or Joomla plugins, the two CMSes supported at that time. Partly as a result of the ethos of the Drupal community members participating in the CiviCRM ecosystem, a larger variety of Drupal modules to integrate with CiviCRM were created and matured. 

Support for running CiviCRM with WordPress was added in 2012, which allowed WordPress plugins to implement custom integrations. 2012 also saw CiviCRM roll out its own native extension system: flexible, cross-CMS support aiming to reduce the development and maintenance effort involved in creating customizations to extend and modify core CiviCRM functionality. In recent years a large number of CiviCRM native extensions have been released, though there continue to be CMS-specific integrations, particularly around permissions and forms. So the reasons for choosing one CMS over another based on the available functionality and integration levels continue to decline.

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

=======
CiviCRM works with several popular open source Content Management Systems (CMS)<sup><a href="#1">1</a></sup>.

-   Drupal
-   Backdrop CMS
-   Joomla
-   WordPress

Before starting a project you should consider how you plan to use
CiviCRM both now and in the future. Two big questions are; How much
information from CiviCRM do we want to display on our website (e.g. A
live list of our current members)? What information, collected through
our website, do we want to store in CiviCRM (e.g. Family data from a
public form with the family relationships automatically added). This is
known as integration. How much integration you require will help you
determine which is the best CMS for you.

You may also choose a CMS because you already know how to use it or it
is already used by your organisation. Each CMS has different advantages
and disadvantages and different people will give you views on which is
better. This chapter gives a quick introduction to each of them.

**Drupal** is the most flexible and has the best integration with
CiviCRM. If you have complex CMS needs or envisage a lot of interaction
between users on your website and your CRM, then Drupal might be the CMS
for you. The main negative is that this flexibility makes it harder for
new site builders to get to grips with.

**Backdrop CMS** is very similar to Drupal 7, with many improvements making
it slick and lightweight. It started as a fork (that is, a copy) of Drupal 7,
aimed at users for whom Drupal 8 was likely to be too complex either to use,
or to migrate to from Drupal 7. Drupal 7 modules and themes require
modifications to work with Backdrop CMS, though many have already been ported
and are actively maintained.

**Joomla** isn't as popular as WordPress, though it is more popular than
Drupal. Joomla is easier to learn than Drupal. The amount of integration
available is lower than then when using Drupal.

**WordPress** is by a wide margin the most popular of the four options.
It is considered to be very easy to build sites using WordPress,
especially for people that are not familiar with web technology. A lot
of people know WordPress as a blogging platform rather than a CMS, but
it is becoming more powerful and flexible with each release. The amount
of integration available is lower than when using Drupal.

Differences at a CMS level are mentioned elsewhere through this guide.

## Users and Contacts

* Anonymous site visitors and those with a website login are website **Users**
* Individuals and organisations with a CiviCRM record are **Contacts**

<sup><a name="1">1</a></sup> Usage of content management systems for websites, [Link](https://w3techs.com/technologies/overview/content_management/all).
