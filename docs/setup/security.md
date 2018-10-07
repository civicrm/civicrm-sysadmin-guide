# Security

CiviCRM is a web based solution, and as such, requires a web server to
run. When web servers store sensitive data and are publicly available
over the Internet - as CiviCRM is designed to be - security is an
important aspect to consider. The recommended approach to securing CRM
data is to use a VPN (Virtual Private Network) to encapsulate data
transferred over public networks in encrypted packets. One of the
simplest methods of implementing this is through forcing the use of
encrypted tunnels when accessing the server through various data
protocols (e.g. SSH, SSL and FTPS, explored below); this effectively
wraps and the data in a protective shell, which can only be opened by
the user's web browser, and the server.

## Encrypted data transfer

There are a number of different protocols (methods) for transferring
data from one point to another.

-   **FTPS**: for uploading files to the server outside of the CiviCRM
    interface (admins only), you may wish to use a FTPS (Secure File
    Transfer Protocol) client. If possible, set up FTPS accounts with
    usernames and passwords.
-   **SSH**: the SSH (Secure Shell) is a method which can be used to
    authenticate a user - by password or a key - and tunnel them into
    the server to execute command-line instructions.
-   **SSL**: this affects your everyday users accessing CiviCRM through
    a web browser. SSL (Secure Sockets Layer) encrypts the data
    submitted on a page by a user and sends it to the server - the only
    entity holding the 'key' to decrypt the data, and vice versa. SSL
    may be required for PCI compliance, or simply to prevent the
    interception of sensitive information during transit. By default,
    CiviCRM is *not* secured for browser access, please read the section
    'Setting up SSL' for configuration instructions.

Note: ensure passwords used by one person across several protocols are
different, as each carry with it varying levels of control.

## Should I use SSL (Secure Sockets Layer)? 

### PCI compliance

American Express, Discover Financial Services, JCB International,
MasterCard Worldwide, and Visa Inc. work together to form standards for
online payment processing (see
[https://www.pcisecuritystandards.org/organization_info/index.php](https://www.pcisecuritystandards.org/organization_info/index.php)).

Hosting for websites that accept payments using CiviContribute and
CiviEvent should comply with their standards if the plug-in you are
using does not route the user to external pages for payment processing
(e.g. PayPal Standard redirects users to PayPal pages to make the
transaction before returning to your website, and should therefore meet
these standards). If credit card information *is* being processed or
stored on your server, there are a number of PCI Payment Application
Data Security Standards (PCI PA-DSS) which must be met, including the
need to use SSL. Each CiviCRM installation must also be tested for PCI
compliance every 4-12 months. Consider the payment processing method you
intend to use carefully before implementing it, use SSL if you are using
a credit card payment processor, and seek security consultation if you
decide to store credit card information on your server (this is not
recommended).

For further information, see:
[https://www.pcisecuritystandards.org/merchants/index.php](https://www.pcisecuritystandards.org/merchants/index.php).

### Unauthorised data access

Aside from the potential need to meet PCI compliance, you should use SSL
if you wish to ensure:

-   your data cannot be read by unauthorised users (e.g. through
    intercepting un-encrypted data during transit between the user and
    server).
-   a user's session is not hi-jacked through acquiring the cookie used
    to authenticate their access. If this occurs, an unauthorised
    individual could assume their identity and proceed to use the system
    through their account.

## Setting up SSL

SSL encrypts the data transferred between a user's web browser and the server. It is *not* enabled upon installation as it requires an SSL certificate. Many browsers now display a warning that a page is insecure if content on it is not delivered via an SSL connection secured by a certificate from a trusted vendor.

Before choosing a hosting company or web server provider, check how they support SSL certificates. If running your own server, you can obtain free certificates from [LetsEncrypt](https://letsencrypt.org/). Control panel vendors may also provide proprietary systems for installing free certificates instead of, or as well as, LetsEncrypt. On shared hosting, free certificates from LetsEncrypt or the control panel vendor can normally be activated by clicking in the relevant part of the control panel, or by raising a support ticket, following documentation provided by the hosting company. CiviCRM does not support serving specific pages via SSL under a different domain than your site's main domain, a feature which some hosts still offer as 'shared SSL.'

All SSL certificates expire, and must be reinstalled before expiry, otherwise browsers will display warnings about the certificate not being valid. Free certificates usually last for only three months, on the assumption that a short life is more secure if the certificate is compromised, and creates no inconvenience because free certificates are are usually installed and renewed automatically. Automated installation and renewal sometimes causes problems not specific to CiviCRM, which is one reason why some users prefer paid certificates, manually installed. Paid certificates can usually be purchased from your hosting company, as well as third-party vendors. On shared hosting they usually have to be installed by the hosting company, or in the control panel, following documentation the hosting company provides.

### Redirecting traffic from http to https

After installing the SSL certificate, traffic to your site should be redirected to use https instead of http. To use SSL for *all* CiviCRM pages, either edit the web server's configuration file (not usually possible on shared hosting), or your CMS's .htaccess file, to force SSL by redirecting all HTTP requests to HTTPS. Sometimes hosting control panels provide a facility for setting up such a redirect, which usually involves automatically writing a few lines of code to your .htaccess file. If using Drupal, bear in mind that every core update by default replaces your .htaccess file with a new copy, and all modifications including any https redirect will be lost and must be re-instated. Without this redirect, users will be able to access the site on both SSL and non-SSL connections (except in the rare case that the server is configured not to work at all on non-SSL connections).

Alternatively, after installing the certificate, enable 'HTTPS' redirection on your CiviCRM site so that just the login, online contribution, member, event and administrator pages use SSL encryption. CiviCRM has an option to check the certificate and enable SSL for these pages at **Administer** > **System Settings** > **Resource URLs**.

If you have a public-facing website, note that when switching traffic from (for example) http://example.org to https://example.org, some analytics programs, such as the Facebook share count, treat http://example.org to https://example.org as separate sites, and re-start the count of page visits or shares for the https version at zero. Workarounds for this issue are not specific to CiviCRM and are outside the scope of this documentation.

## Backups and their security

All computer systems are prone to failures - both hardware and software.
It is advisable to create periodic backups of all existing data (and
possibly the software) to fulfil two important purposes: recovery and
retention. In regards to recovery, the organisation can ensure that the
data gathered and stored in its database is not lost in the event of
failure. Backups can also aid in the strengthening of service
continuity. In some situations it is essential that data gathering or
analysis operations do not cease, and the ability to build a working
tool from a backup (while the issue is being addressed) minimises
downtime. Conversely, retention is useful when the organisation needs to
check the state of data gathered at a given time in the past.

Once made, the backups themselves must also be secured from natural
disasters, fire, vandalism and theft. It is good practice to encrypt
backups and duplicate them, keeping one copy on premises, and sending
the other to storage at another external location.

## Data storage jurisdiction

CiviCRM can be run on a web server managed by your organisation, or by
an external hosting provider. When working with issues related to human
rights, or if your organisation is gathering sensitive information about
a country's government or its officials, it may be important to know
where your data is stored. Consider gathering detailed information about
where the servers are physically located, and the country whose
jurisdiction the data will fall under in case a governmental agency
requests information.

## Other security concerns

Data may be accessed by unauthorised individuals through a
variety of methods, many of which do not directly relate to the CRM's
security. Amongst others, the following are areas that should be
examined:

-   Physical access to the server: the building holding the servers
    should be adequately protected against break-in.
-   Passwords: an organisational policy should be implemented where
    passwords are of a sufficient complexity and length (e.g. minimum 8
    characters, including letters and numbers), and must not be shared
    or divulged over insecure communications like email.

## Filesystem permissions

### Directories should not be browsable

In its default configuration, CiviCRM places some uploaded and server-generated data in the CMS's data folder (such as Drupal's "sites/default/files" or Joomla's "media"). This folder is web-accessible, but many of the documents processed by CiviCRM should not be web-accessible. If CiviCRM's data folders are not suitably protected from web access, then sensitive information may be disclosed.

If these directories are accessible and browseable, then data from your site may be indexed by search engines. This makes it significantly easier for an attacker to identify your site.

### Uploads should not be accessible

If you see this warning, CiviCRM is alerting you that it was able to retrieve a file from the CiviCRM uploads directory over HTTP. If this is the case then there is a risk of personal data being exposed via your CiviCRM install.

Files in this directory need to be accessible to CiviCRM via local file reference, but may contain personal data and therefore should not be accessible over the public internet.

The correct response on seeing this warning is to attempt to confirm whether the warning is valid by accessing files directly yourself. If this is possible, then server configuration adjustment is required to prevent direct access of those files.

To do this -

1. Identify the full URL path to your CiviCRM uploads directory.
_For example: the CiviCRM Drupal Demo site is configured to use /var/www/drupal.demo.civicrm.org/public/sites/drupal.demo.civicrm.org/files/civicrm/upload/ as the upload path, and /var/www/drupal.demo.civicrm.org/public/ is the webroot, so the full URL to the uploads directory is http://drupal.demo.civicrm.org/sites/drupal.demo.civicrm.org/files/civicrm/upload/_
1. Identify the name of a file in that upload directory.
_To continue the example, I'd look in that directory on the server, see that "SomeUpload_1122334455667788.csv" exists, and add that to the URL, resulting in http://drupal.demo.civicrm.org/sites/drupal.demo.civicrm.org/files/civicrm/upload/SomeUpload_1122334455667788.csv_
1. Attempt to directly visit this URL. If I am able to view the contents of the file, then additional server configuration is required.

Refer to your server administrator or hosting organisation for support in configuring your webserver, as webserver configuration details differ between servers and hosting environments.

CRM_Utils_Check_Security::checkUploadsAreNotAccessible() is defined in [CRM/Utils/Check/Security.php](https://github.com/civicrm/civicrm-core/blob/master/CRM/Utils/Check/Security.php#L168) - see this file for implementation details.

See also CRM_Utils_Check_Security::checkDirectoriesAreNotBrowseable()

This check was introduced in CivICRM 4.4.4. For details, see [https://civicrm.org/advisory/civi-sa-2014-001-risk-information-disclosure](https://civicrm.org/advisory/civi-sa-2014-001-risk-information-disclosure)

If you believe you are seeing false positives on this check, please see [http://forum.civicrm.org/index.php?topic=31570](http://forum.civicrm.org/index.php?topic=31570.new;topicseen#new)


### The Log file should not be accessible

The CiviCRM log file contains a significant amount of debug data on some sites. If directly accessible, sensitive data may be revealed to outsiders. This file should be protected.

Until some direction can be given, more info can be found at this link:

[https://civicrm.org/advisory/civi-sa-2014-001-risk-information-disclosure](https://civicrm.org/advisory/civi-sa-2014-001-risk-information-disclosure)
