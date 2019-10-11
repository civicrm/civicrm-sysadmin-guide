# Hosting

Before installing CiviCRM, careful consideration must be given to where
it will be hosted. The main options with an outline of the situations in
which you would want to choose them, and the advantages and
disadvantages of doing so are outlined below:

## CiviCRM Spark {:#spark}

[CiviCRM Spark](https://civicrm.org/spark) is a quick and secure way to get started with CiviCRM. It allows you to get your own installation of CiviCRM up-and-running in minutes. Spark is ideal for organizations of any size wanting to test and trial CiviCRM, or for smaller organizations and activities with up to 2000 contacts.

## Recommended hosting providers and implementers {:#experts}

There are [implementers and experts](https://civicrm.org/providers) in the CiviCRM community able to
manage the hosting and/or installation for you. If requested, they may
also be available to manage a local implementation and configuration on
your premises.

## Internal hosting

If you have an internal IT department or staff members with a technical
background, you may wish to host CiviCRM internally. To do this, you
will need:

-   Servers or dedicated PC hardware available to run as a web server,
    24x7.
-   A space on premises to permanently store the hardware, possibly air
    conditioned.
-   An un-interrupted power supply (UPS) to ensure the server is still
    available during power outages.
-   An internet connection suitable for hosting a website (static IP, sufficient
    bandwidth, etc).

If hosting internally, it is important to note that the system will be dependent
on internet connectivity to your server, and that in event of an outage only
external users and visitors will lose access. This might prevent visitors
accessing contribution forms or widgets embedded on external sites.

Other considerations are the secure partitioning of this internally-hosted
website from other services on the same machine, security of network, and other
factors including the cost of management and maintenance. It's recommended to
have some expert guidance on doing this right.

## External hosting

With internal expertise you could manage the install and configuration
in-house, but host CiviCRM with an external provider. In this instance,
we recommended you rent a VPS (Virtual Private Server) to ensure you
have complete control of all packages and libraries (e.g. PHP, MySQL,
etc), and are therefore able to configure it to your specific
requirements.

Many shared hosts restrict the level of access you have, and may not
support CiviCRM if you are unable to install the required
pre-requisites. Shared hosts can also be prone to performance issues, as
the hardware is shared between a group of customers with varying usage
levels; if one customer's website suddenly receives a large spike in
traffic, every website on that server could experience a lengthy outage.

Disadvantages aside, leasing space on a shared host is typically cheaper
than a VPS, and both are subscription services on a monthly or annual
basis (discounts may be available for longer leases.

**We advise that you trial run any service for a short-term before
committing to a longer period.**

## Existing hosting

If you are already using a website host, contact your provider to
determine whether they support the packages and libraries required by
CiviCRM. If they do not, there are two options available to you:

### Migrate to another host

If your current host cannot accommodate CiviCRM you may want to consider migrating your CMS to a new host before installing CiviCRM. Depending on the CMS you are using, the process of moving from one host to another may be fairly straightforward. You are in a good position to transfer to another host if you can: 

1.  request that your users **stop creating and updating content**
    during the migration,
2.  **export and import** all of the content from/to the chosen CMS,
3.  **edit your DNS records** to switch the 'pointers' to your
    website from the old host to the new host

### Run the website and CiviCRM in parallel, on different servers

If you cannot move your website to a different host, you could purchase
a second account on a host capable of running CiviCRM, and run the two
systems alongside each other.

In this instance, you would use a CNAME DNS record to point to a second
copy of the CMS and CiviCRM on the other host (e.g. `civicrm.example.org`),
and link to it from your website, perhaps in form of a log-in button.

Aside from paying a second bill, one of the limitations to this approach
is the need to clone the style of your website on the second host to
give the visitor the illusion that they are in the same place. If
changes to the style are changed, the work must be duplicated.
