# Email system configuration

This chapter covers system configuration necessary so that CiviCRM can
send and receive email. This is a complex task which requires system
administrator level skills. The correct configuration of your email
system is crucial to keep your server off spam lists and black lists.

See the Email sections chapter on Set up for tasks necessary to set up
the sending of messages once these email system configuration settings
have been configured.

Some parts of the configuration are core CiviCRM functionality (basic
sending and receiving of emails) whereas others (mass mailings) require
that the CiviMAIL component be enabled.

You will need to be able to change the configuration of your DNS, create
email accounts, configure a cron job, read the headers of email
messages, and possibly change the configuration of your SMTP server.

This chapter assumes you are running CiviCRM on a Linux server and that
you are comfortable working with the shell and running some simple
commands. Most of these steps will be similar on other operating
systems, but you will need to adapt them to your system and tools.

The configuration described works fine for mailings to up to about
10,000 people. If you plan on sending email to hundreds of thousands of
contacts, you should benchmark several options and consider a dedicated
SMTP server.

In this chapter we'll use an external Gmail mailbox address to test
configuration. So the first step is to create a Gmail account if you
don't have one already; alternatively, you can use another address for
testing the procedures in this chapter, but you will need to be able to
view the source of the mails you receive.

Once your system is properly configured we are going to use cron to
trigger CiviCRM's **Scheduled Jobs** to ensure your scheduled mailings
are sent.

## Configuring outbound email service

Outbound email settings are configured at: **Administer > System
Settings > Outbound Email (SMTP/Sendmail)**. The choices here are:
!!! note
    Consult your server administrator to find out which one is best for your system.

-   **mail()**: This is the default option.
-   **SMTP**: If you have a dedicated external mail server, specify its
    details here. Bounce messages generated with SMTP are slightly more
    complete than the ones from mail().
-   **Sendmail**: This option is kept for compatibility with older CiviCRM
    versions.
-   **Disable Outbound Email**: Works as expected.
-   **Redirect to Database**: All emails will be recorded as archived mailings
    instead of being sent out. They can be found in the civicrm\_mailing_spool
    table in the CiviCRM database.
-   Other options can appear here by installing extensions to support additional
    mail providers - check the [CiviCRM Extensions Directory](https://civicrm.org/extensions/)
    for providers you can use.

After making a choice, send a test email to your account on Gmail and
verify that you receive it.

If you receive the following error message, you'll need to configure a
default FROM email address (covered in the chapter on CiviMail
configuration).

```
Sorry. A non-recoverable error has occurred.
The site administrator needs to enter a valid 'FROM Email Address' in
Administer -> Configure -> Domain Information.
The email address used may need to be a valid mail account with your email service provider.

```

Once you have received the email, you will need to view the source.
This is done in Gmail by clicking on "Show original" in the email you
receive.

The email should contain headers that resemble the following.

```
Received: from yourmailserver.example.org (xxx.example.org
[12.45.120.30])
by mx.google.com with ESMTP id e31si4519230wej.3.2010.04.26.00.38.16;
Mon, 26 Apr 2010 00:38:17 -0700 (PDT) Received-SPF: pass
google.com: best guess record for domain of
[youremail@example.org](mailto:youremail@example.org) designates
12.45.120.30 as permitted sender) client-ip=12.45.120.30

```

In particular:

*  "Received: from" header should correspond to your mail server and be
properly configured. It might contain information about your hosting
provider instead of your domain name. This is not a problem as long as
the mail server is properly configured. If you have a dedicated IP
address for your server, you should try to configure a reverse DNS that
represents your organization instead of the default name.

*  "Received-SPF" header should list "pass" or "neutral". Sender
Policy Framework is described later in more detail.

Sending mass mailing is resource intensive. We don't recommend sending
email messages from budget hosting providers. The time you will spend
troubleshooting will often cost more than upgrading to a more
professional host. Check with your hosting provider to find out whether
they limit the number of email messages you can send and whether they
run PHP in safe mode.

Some of your recipients' mail servers use DNS based blacklisting
services (DNSBL) which keep a blacklist of IP addresses likely to send
spam. Mail from these servers will be flagged as spam and not reach its
intended destination. If your server is blacklisted (for instance,
because enough of your recipients flagged your email as spam, or because
another website on your server has been flagged as spam), you will need
to contact the organizations that have blacklisted you and convince them
to remove you from their list.

They are several websites that help you testing whether you are in a
DNSBL. A web search for "blacklisting email" will turn some up. Test
regularly to find whether you are on a blacklist.

## Configuring inbound email processing

This section explains configuration for bounce processing and auto
filing incoming emails. Configuring **Scheduled Jobs** to do the actual
bounce processing is discussed later in this chapter.

!!! note
    Bounce processing (and auto-filing) is available for any mail sent with the CiviMail component. For other emails sent by CiviCRM (such as confirmation emails in CiviEvent), those features are not available.

### Bounce processing

CiviCRM can automatically receive the [bounced email
notification](http://en.wikipedia.org/wiki/Bounce_message) and, based on
the [type of bounce](http://tools.ietf.org/html/rfc3463) reported by the
recipient's server, flag your contacts accordingly. To accomplish this
you will need to set up an email mailbox to receive bounced email
messages and a schedule the **Bounces Fetcher** job that will
periodically read this mailbox and update your contacts in CiviCRM.

1. Go to **Administer > System Settings > Scheduled Jobs**.
1. Find the "Fetch Bounces" scheduled job and enable it.

The bounce email address is an "invisible" email address visible only in
the email message's envelope (hidden fields that precede the headers and
message added by the user). Choose any name you like that is meaningful
to you. In this example we have chosen *return*, so the email address we
need to set up on a mail server for example.org is *return@example.org*.

For each email sent via CiviMail's mass mailing feature, a new unique
sender address is created using the [variable envelope return path (VERP)](http://en.wikipedia.org/wiki/Variable_envelope_return_path).
When a message bounces back to CiviCRM, this unique sender address can be
used to identify the contact it was originally sent to.

CiviCRM then looks at the bounce pattern and type to decide what action
to take. Bounce types fall into two basic categories: permanent failures
(hard bounce) and transient failure (soft bounce). A single
permanent failure triggers CiviCRM to set the contact's email as On
Hold. For transient failures, CiviCRM waits for several bounces before
setting the contact's email On Hold.

The specific [threshold for each bounce
type](/setup/civimail/inbound.md#bounce-handling)
can be found in the civicrm_mailing_bounce_pattern and
civicrm_mailing_bounce_type. Multiple different bounce reply patterns
are linked to a given type and threshold.

### VERP support

If your mail delivery system does not support sending via VERP addresses, you
may see an "Invalid address" error when sending mails. Either switch to a mail
provider which does support this, or disable VERP.

-   There are plenty of [mail services which fully support CiviCRM features](https://wiki.civicrm.org/confluence/display/CRM/Mailing+providers) -
    consider using one!
-   VERP can be disabled via the option **Track replies using VERP in Reply-To
    header** at **Administer > CiviMail > CiviMail Component Settings**. This
    will reduce CiviCRM's ability to correctly match bounced messages.

### Email-to-Activity processing

CiviCRM can automatically retrieve email from a specified inbox and file
it as an email activity against contacts of type Individual corresponding to
sender and recipients of the email. New individual contacts are created for
email addresses not already assigned to individuals in the database.

**NOTE**: These features only work for the Individual contact type. If the
incoming email comes from an email address already recorded against an
organization, a new individual contact with that same email address will be
created and the activity will be recorded against that new individual contact,
not against the organization.

There are two ways to do this (either or both ways can be setup at same
time):

-   **Special email address:** Set up a special email address for your
    organization, e.g. civiemails@example.com. Users can then add this
    address in the Bcc field for your outbound emails; they will get
    auto filed in CiviCRM as described above. No one who receives the
    email will see this special address if the Bcc field is used.
-   **IMAP folder:** Set up a folder in your IMAP Inbox where you can
    drag emails that you want filed in CiviCRM. This works with both
    inbound and outbound emails (this requires that your email be set
    up using IMAP.)

### Allowing users to edit inbound e-mails

Activities created by CiviCRM as a result of email-to-activity processing 
are not editable by users, as there is a restriction enforced on the Inbound 
Email activity type. To allow users to be able to edit these activities, an
administrator can enable the **CiviCRM: edit inbound email basic information** 
or **CiviCRM: edit inbound email basic information and content** permissions
for the roles that should be able to edit the activities.

**CiviCRM: edit inbound email basic information** will allow users to edit every
field of the activity, except the original message, stored int the activity's 
details.

**CiviCRM: edit inbound email basic information and content** will allow users
to edit every field of the activity, including the original message's content.

### Special email address for incoming email

There are several ways of configuring your incoming mailbox:

-   **Sub-addressing:** Your mail service might allow you to append a
    +*tag* or -*tag* qualifier to your e-mail address (e.g.
    *return+test@example.org*). Several mail servers, including Gmail,
    Yahoo! and Postfix provide this sub-addressing by default.

    Try to send yourself an email, adding a +*tag* or -*tag*. If you
    received the mail you sent with a tag, it means that you can
    directly use the mailbox you created (*return@example.org* in our
    example) as the [VERP](http://en.wikipedia.org/wiki/Variable_envelope_return_path).

-   **"Catch-all" account:** If sub-addressing doesn't work on your mail
    server, you need to define the mail account you created
    (*return@example.org*) as the "catch-all" account. Every mail sent
    to an address that isn't a real mail account will end up there,
    including all the bounced email messages.

-   **External address**: If neither of the preceding methods works,
    consider creating a new account on a service such as Gmail and use
    it to receive the bounced emails. You will have to set filters in
    this account so it doesn't discard as spam all the bounced email it
    will receive.

### Adding an incoming email account for processing bounces and/or email-to-activities

Once you have created your email account to receive bounces or emails
for auto filing, you need to set up CiviMail so it knows how to read it:

1. Navigate to **Administer > CiviMail > Mail accounts**.
1. Identify the default mail account used for bounce processing, and **edit** that account. 
1. Specify the mail **server, username, and password** you used when creating the account.
1. The **local part** is optional and only relevant if you were able to set up an account using sub-addressing. It should be the account you created with '+' or '-' appended , e.g., "return+" or "return-".
1. The **email domain** is the domain of your email address (example.org).
1. You can leave the **return path** empty.
1. If your mail server supports it, specify IMAP **protocol** and check **SLL**, otherwise use POP.
1. You can specify an IMAP folder in the **source** field using the syntax INBOX.CiviMail. **Note:** Some exchange servers may not be configured in a compatible way. In that case, you can configure a script like fetchmail and use Maildir.
1. In the **Used for?** field you can choose whether you want to use the email account for **Bounce Processing** or **Email-to-Activity Processing** (or Auto filing). You can have multiple accounts specified for auto filing but only one for bounce processing. This will be marked as default.

Once the **Bounce Processing** mailbox is configured, you will need to
configure CiviMail to empty it, read all these bounced messages and
identify the related bounced contacts. This is performed by the
Scheduled Jobs feature of CiviCRM .

We recommend testing the bounce process by running the process manually
before setting up CiviCRM to process the bounced email messages
automatically. This can be done in **Administer > System Settings >
Scheduled Jobs**. Locate **Fetch Bounces** and select **more > Execute
Now**. Check the Job Log for any error messages.

Once you have verified that CiviCRM can properly handle the bounce, you
can set it up to automatically process the replies and bounces on a
regular basis.


## Scheduling inbound and outbound mail processing

As discussed in the earlier chapter, mail processing and other jobs may
be automated through the [Scheduled Jobs](https://docs.civicrm.org/user/en/latest/initial-set-up/scheduled-jobs) administrative page, [cron](/setup/jobs.md#cron) or a
combination of both. Below are specific examples for enabling CiviCRM mail processing.

### Scheduling using the Scheduled Jobs administrative page

Mass mailings are generated via the CiviMail web interface and are
queued to be sent to their recipients. To schedule the regular
processing of this queue and any bounces that are returned go
to **Administer > System Settings > Scheduled Jobs** and find
the **Fetch Bounces** and **Send Scheduled Mailings** jobs. Edit each in
turn, setting their scheduled **Frequency** to "Hourly," "Daily" or
"Every time cron is run." The defaults should be fine for small
installations. Then select **more > Enable** for each.

In this example using the jobs' default frequency and [cron configured
to run at 15 minute
intervals](/setup/jobs.md),
scheduled mailings would be sent every 15 minutes and bounces would be
fetched and processed hourly.

If you need to send some email from CiviCRM right away, without waiting
for the cron job, you can trigger the sending process by
visiting **Administer > System Settings > Scheduled
Jobs **select **more > Execute Now**. Use this capability sparingly. It
could utilize a lot of server resources and cause CiviCRM to slow down
noticeably. The administrative settings for sending email are usually
configured to minimize the load on the server, and the scheduling the
job is a more efficient way to send mass email.

### Scheduling only the mail jobs using the command-line interface

As an alternative on Linux and other Unix or Unix-like systems,
individual cron jobs may be used to schedule inbound and outbound mail
processing rather than the **Scheduled Jobs** page. This approach gives
experienced system administrators more granular control over these
processes by configuring specific cron jobs apart from other scheduled
jobs.

The cron job needs to run using an account recognized by your CMS.
Create an account dedicated to this task (e.g., *mailprocess, civimail,
etc*), give it a long, secure password (e.g. *seol-lzprm42amv-psyc*) and
grant it access to CiviCRM, CiviMail, and view all contacts. Do not
change the account password without changing the password in the
configuration files of this cron job.

To set up your cron job, first, find out whether php-cli is installed.
From the shell, type *php -v*. If you see **(cli)** in the result, as
in:

```
PHP 5.2.3-1ubuntu6.5 (cli) (built: Feb 11 2009 19:55:53) Copyright (c)
1997-2007
The PHP Group Zend Engine v2.2.0, Copyright (c) 1998-2007
Zend Technologies with eAccelerator v0.9.5.3, Copyright (c) 2004-2006
eAccelerator, by eAccelerator

```

This means you have php-cli installed and you should use it, because it
has several advantages:

-   You can run a PHP script at a lower priority than your web server,
    so that even if it takes a lot of CPU, it won't interfere with the
    regular users of your site.
-   You can set different memory limits for the php-cli process and the
    PHP process used by your web server.
-   You avoid the overhead of the web server and the HTTP layer.
-   You won't have any timeout problems.

The following is complete cron configuration to handle CiviCRM's mail
requirements:

```
# This must be set to the directory where civicrm is installed.
CIVI_ROOT=/var/www/civicrm

# Comment: I believe these two lines are unnecessary.
# USER=www-data
# MAILTO="you@example.org"

# Location of the PHP Command Line Interface binary.
# nice -19 forces to run at a lower priority than the web server

PHP=nice -n19 /usr/bin/php

# line to be modified according to the informations below
# like this: PARAMS= -j -s<default or domain> -u<user>
-p<password> -e Job -a process_mailing

PARAMS= -j -sdefault -umailprocess -pseol-lzprm42amv-psyc -e Job -a
process_mailing
PARAMSBOUNCE= -j -sdefault -umailprocess -pseol-lzprm42amv-psyc -e Job
-a fetch_bounces

# cronjob send
# m h dom mon dow command
*/5 * * * * cd $CIVI_ROOT; $PHP bin/cli.php $PARAMS
*/15 * * * * cd $CIVI_ROOT; $PHP bin/cli.php $PARAMSBOUNCE
```

The user that run the scripts (*www-data* in this example) needs to be
able to write into the temporary folder. Your configuration might
specify a different user.

You don't have to run both scripts at the same frequency. The preceding
crontab file verifies every 5 minutes whether mail messages need to be
sent, but only every 15 minutes whether bounced email needs to be
processed.

**PARAMS** contains:

1.  The site you used, which is **-sdefault** on Drupal. If you run
    multiple CiviCRM sites on a single server, you need to specify your
    site's domain, such as **-sexample.org**.
2.  The user login account (**-umailprocess**).
3.  The password you defined (**-pseol-lzprm42amv-psyc**).


## CiviMail Installation

CiviMail installation consists of four parts: enabling the CiviMail component, setting up the SMTP configuration options, defining a cron job that will actually send the scheduled mailing, and choosing and setting up the return email channel.

If you want to have an email system that works properly we **strongly recommend you read this entire chapter.** Skipping over any of these steps can result in high bounce rates, which can get your domain blacklisted. If after following these instructions your email system does not work, please read this chapter again. An older, yet more detailed version of these instructions, can be found at [http://en.flossmanuals.net/CiviCRM/SystemConfiguration](http://en.flossmanuals.net/CiviCRM/SystemConfiguration)[http://en.flossmanuals.net/civicrm/ch042_system-configuration/](http://en.flossmanuals.net/civicrm/ch042_system-configuration/)

### Setting Up CiviMail

If the CiviMail component is not enabled, go to CiviCRM > Administer > System Settings > Enable CiviCRM Components and enable CiviMail.

Make sure your Organization Address and Contact info is set under CiviCRM > Administer > Communications > Organization Address and Contact Info.

Go to CiviCRM > Contacts > Manage Groups and click the "settings" link for the group you want to make available to CiviMail. Check the box that says "Mailing List". Save your settings and repeat for each additional group you would like to send mailings. If you do not complete this step, you will not see any groups when you try to send a mailing.

### CMS User and Role

For CiviMail to work in your environment you must create a CMS user with permissions to

* view all contacts
* access CiviCRM
* access CiviMail

You can use an existing CMS user and role however we recommend that you use a dedicated user and role (name them something like 'mailsender')

### Setting Up the SMTP Configuration

Outbound email setting are configured at: Administer > System Settings > Outbound Email (SMTP/Sendmail). The choices here are:

* mail(). This is the default option and if it works for you, you should use it.
* SMTP. If you have a dedicated external mail server, specify its details here. Bounce messages generated with SMTP are slightly more complete than the ones from mail(), but there is no practical benefit to using SMTP if you can use mail().
* Sendmail. This option is kept for compatibility with older CiviCRM versions.
* Disable Outbound Email. Works as expected.

### Setting Up the Cron Job

To have your mailing actually send, set up a cron job that will periodically poke CiviMail to send all of the mailings that are past their scheduled date. **See [Managing Scheduled Jobs](/setup/jobs.md) for more details**.

There are two methods to run the cron: From a Control Panel or from a command line interface (CLI) which is the preferred way.

#### **From a Control Panel**

1. Open up the Crontab in your control panel
1. Select the frequency with which you want this cron job to run
1. Replace the '[siteroot]', 'name' 'pass', and 'key' with the information from your site:

#### From a CLI Command Line

1. Simply login and type: _crontab -e_ which will usually load the crontab for the current user using the vi or vim text editor
1. If you prefer nano as an editor, you can type _env EDITOR=nano crontab -e_ on servers where nano is installed

#### Using the shell (CLI PHP) via Crontab

Make sure php-cli is installed by running the following command in the shell:

```
php -v.
```

If you see **(cli)** in the returned result, as in:

```
PHP 5.2.3-1ubuntu6.5 (cli) (built: Feb 11 2009 19:55:53)
Copyright (c) 1997-2007 The PHP Group
Zend Engine v2.2.0, Copyright (c) 1998-2007 Zend Technologies
with eAccelerator v0.9.5.3, Copyright (c) 2004-2006 eAccelerator, by eAccelerator
```

This means you have php-cli installed and you should use it instead of a control panel because it offers several advantages:

* You can run a PHP script at a lower priority than your web server, so that even if it takes a lot of CPU, it won't interfere with the regular users of your site.
* You can set different memory limits for the php-cli process and the PHP process used by your web server.
* You avoid the overhead of the web server and the HTTP layer.
* You won't have any timeout problems.

```
# This must be set to the directory where civicrm is installed.
CIVI_ROOT=/var/www/civicrm

# Comment: I believe these two lines are unnecessary.
# USER=www-data
# MAILTO="you@example.org"

# Location of the PHP Command Line Interface binary.
# nice -19 forces to run at a lower priority than the web server
PHP=nice -n19 /usr/bin/php

# line to be modified according to the informations below
# like this: PARAMS= -j -s<default or domain> -u <user> -p <password> -e Job -a process_mailing
# Sample data here below.
# Create a CMS user named "mailprocess" or the like with proper permissions to send mailings
# and put here its credentials (-u and -p options).
# Blankspaces matter.
PARAMS= -j -sdefault -u mailprocess -p password -e Job -a process_mailing
PARAMSBOUNCE= -j -sdefault -u mailprocess -p password -e Job -a fetch_bounces

# cronjob send
# m h dom mon dow command
*/5 * * * * cd $CIVI_ROOT; $PHP bin/cli.php $PARAMS
*/15 * * * * cd $CIVI_ROOT; $PHP bin/cli.php $PARAMSBOUNCE
```

The user that run the scripts (_www-data_ in this example) needs to be able to write into the temporary folder. Your configuration might specify a different user.

You don't have to run both scripts at the same frequency. The preceding crontab file verifies every 5 minutes whether mail messages need to be sent, but only every 15 minutes whether bounced email needs to be processed.

**PARAMS** contains:

1. The site you used, which is **-sdefault**. If you run multiple CiviCRM sites (applicable for Drupal) on a single server, you need to specify your site's domain, such as **-sexample.org**.
1. The user login account ( **-u mailprocess** ).
1. The password you defined ( **-p password** ).
1. [The scheduled job](/setup/jobs.md) (-a)

#### Using wget via Crontab

**wget** will process an external URL (like CiviCRM's cron.php) and therefore will send CiviMail messages. You can run this script using either "GET" or "POST" methods. However, using "POST" with the authentication values stored in a file on your server is generally considered to be better security practice, especially on shared hosting.

#### wget with GET method

Depending on your CMS, simply enter a single like like so, substituting in your login credentials and sitekey.
 Note that the GET method is not recommended on shared hosting because the credentials are written to the access logs.

```
Joomla:
wget -O - -q -t 1 \
'http://example.org/administrator/components/com_civicrm/civicrm/bin/cron.php?name=username&pass=password&key=site-key'

Drupal:
wget -O - -q -t 1 \
'http://example.org/sites/all/modules/civicrm/bin/cron.php?name=username&pass=password&key=site-key'

WordPress:
wget -O - -q -t 1 \
'http://example.org/wp-content/plugins/civicrm/civicrm/bin/cron.php?name=username&pass=password&key=site-key'
```

#### wget with POST method

* Login via shell and create a file named **civicrm-wgetrc** in a _private_ directory such as your user's home directory - **not** in the public web root
* Add a single line to this file that contains the three login credentials necessary to run the cron.php script, like so:

```
post-data=name=username&pass=password&key=site-key
```

* Access your crontab with the command _crontab -e_ (or via control panel) and this on a single line:

```
*/10 * * * * WGETRC=civicrm-wgetrc wget -O - -q -t 1 http://www.example.org/sites/all/modules/civicrm/bin/cron.php
```

* Make sure that the crontab working by scheduling a test message to go out to a small mailing list (such as you and your colleagues). Wait several minutes to ensure the message is sent. Note that the 'send test message' function in CiviCRM is not a proper test of the cron

#### Having trouble with cron sending automatically?

You can also test trigger the cron script by navigating to this URL with your browser:

```
Joomla: http://example.org/administrator/components/com_civicrm/civicrm/bin/cron.php?name=username&pass=password&key=site-key

Drupal: http://example.org/sites/all/modules/civicrm/bin/cron.php?name=username&pass=password&key=site-key

WordPress: http://example.org/wp-content/plugins/civicrm/civicrm/bin/cron.php?name=username&pass=password&key=site-key
```

If the above works, it means CiviCRM is working OK, but the cron on your server is not configured properly. Try _crontab -e_ and review it again.

**Note:** On certain server configurations, it may be necessary to specify the exact location of the file, or to add the user to the cron. Example:

```
WGETRC=/full/path/to/civicrm-wgetrc
*/5 * * * * yourLinuxUserName wget -O - -q -t 1 http://www.example.org/sites/all/modules/civicrm/bin/cron.php
```

If you are worried CiviMail won't send email at all, you can send queued messages as well with these commands. You need to be logged in with sufficient privileges to run this command. If nothing is sent, check your Outgoing Mail Settings.

```
Drupal: http://example.org/index.php?q=civicrm/mailing/queue&reset=1

Joomla: http://example.org/administrator/index.php?option=com_civicrm&task=civicrm/mailing/queue&reset=1

WordPress: http://example.org/wp-admin/admin.php?page=CiviCRM&q=civicrm/mailing/queue&reset=1
```

## Setting Up the Return Channel

### Do You Need the Return Channel?

If you send emails and don't manage the bounce, you will be flagged as a spammer at one point or another. If you don't want that, you do want a return channel.

The return channel is required at least in two cases.

First, the return channel is required to track bounces and have the bouncing emails put on hold, as well as to track replies; if you don't need this functionality (you will still get the replies, you just won't be able to have stats about them), you don't need return channel handling.

Second, the return channel is required if you want to handle SMTP (i.e., email-based) actions such as (un-, re-)subscribes and opt-outs - note that these actions have their HTTP (i.e., link-based) equivalents.

### Possible Solutions for the Return Channel

There are four possible solutions for handling of the return channel.

1. The current supported CiviCRM solution is [Email Processor](https://wiki.civicrm.org/confluence/display/CRMDOC/CiviMail+Processor), which polls the email account specified in Administer CiviCRM → Mail Accounts in defined intervals.
1. Finally, the easiest and most hands-free (albeit paid) solution is to setup an [CiviSMTP account](http://forum.civicrm.org/?topic=4459).

!!! warning

    There are two important limitations on your ability to track bounces via return channel. First, as gleaned from [this forum thread](https://forum.civicrm.org/index.php%3Ftopic=7553.0.html#msg38766), bounce tracking does not work when using the **sendmail** delivery method. Second, some SMTP services (notably google) insert their own **Return-Path** address headers, superseding the headers set by civiMail and effectively disabling bounce tracking.

For Google Apps, however, see these [directions on setting up the return channel](/setup/civimail/inbound.md#return-channel-google-apps).


## Troubleshooting

* The page on [Managing Scheduled Jobs](/setup/jobs.md) has a troubleshooting section with solutions for common problems that arise.
* Not receiving the test email? Make sure your FROM address (Administer > CiviMail > From Email Addresses) matches your domain or is recognized as an email account by your ISP.



## Domain Information


### Edit Domain Information Overview

Domain Information is modified to allow changes to your Domain Information. This is used by CiviMail to include the physical address of your organization.

If you are hosting multiple installations of CiviCRM on a single server, refer to the Multi-site Installation documentation.

### Edit Domain Information

Go to **Administer CiviCRM** , In the **Communications** section, click on **Organization Address and Contact Info**.

!!! danger

    Only modify Domain Information if you are aware of the impact of the changes you make!


In the Edit Domain Information page type in the Domain Name, Description, Contact Name, Email Domain and Send Emails Return-Path.

Fill in the Primary Location information.

!!! note

    Set the Email Domain to your organizations domain (e.g. example.org). Enter Location Address information as you want it displayed in CiviMail mailings (when the required {domain.address} token is used).


Click Save to save the Domain Information, Cancel to cancel editing.

If successful, you will see the message

> The Domain "Domain Name" has been saved.

The Domain information will be listed. You can use the **Edit Domain Information >>** link at the bottom of the page for further edits.


## CiviMail in a multisite

When setting up CiviMail for a multisite you should set up a Cron for each site. The reason for this is that the URL that is in any links in the mailing should point to the correct site. Some time ago it used to affect who received the mail too but an MIH for multisite mailings around 4.0 fixed this so that the mailing recipient list is generated when the CiviMail is generated - so that the creator's permissions are applied.

Note that the civimail crons must be run per site but other crons like the one to send membership reminders should be run once for all sites. e.g run job.execute from one site & process_mailing from all (probably also the process_bounces - need to confirm)
