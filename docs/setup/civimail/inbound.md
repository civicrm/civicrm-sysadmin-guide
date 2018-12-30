# Handling inbound mail

## Bounce Handling

### Mechanics of CiviCRM "bounce" handling

When an e-mail message "bounces", that means the recipient e-mail address is temporarily or permanently undeliverable, or, the receiving e-mail server has refused the e-mail message.

CiviCRM's bounce handling is based on the fact that mail servers, when an e-mail can't be delivered to a recipient for one or another reason, return the mail to an address specified in the mail headers. CiviCRM adds that return address to all mailing messages. That address contains a unique id representing that precise message, like 'id.1234@domain.org'. CiviCRM also supports VERP, variable envelope return path. As an option, VERP-handling can be utilized by adding a local part to the return address, giving return addresses of the form 'bounce+id.234@domain.org'. In any case, every message will have a unique return address, so that the message can be identified.

There must exist a real working mail account for these returned mail messages, so you need to create, or have, a real mail account for this purpose. A name like 'bounce@domain.org' or 'returned@domain.org' would be appropriate. If the mail service for this mail account features VERP-handling you should use that. In this case your account should be named 'bounce@domain.org' and the VERP handling will ensure delivery of all messages to 'bounce+<anything>@domain.org' to that account. If not, your only option is to use a catch-all address. This means that all mail messages to unknown addresses for the domain, like 'id.1234@domain.org', ends up in a single, designated catch-all mailbox. This is necessary since all messages have unique return addresses. (Note that this includes all spam mails sent to random addresses in the domain.)

The bounce discovery job will regularly log into the mail account and process all messages having arrived. These messages can be traced to a particular message to a specific contact. That message is recorded as bounced. Statistics of bounced mails can then be reported for a mailing, and problematic contacts identified.

To get this working you need to:

* Get a bounce email account and enable VERP or catch-all for it
* Configure a mail account for bounce handling in CiviCRM Mail Accounts
* Configure mailings to use VERP or not

### Finding "On Hold" contacts

1. From **Search > Advanced Search** and choose **Email On Hold** from the Basic Criteria section at the top
1. Use **Search > Search Builder**.  Then choose **Contacts, On Hold, Primary, =, Yes**

### Causes of e-mail address bounces

When the recipient's email server responds with a "bounce" email it could be for a variety of reasons. The CiviCRM mail processing script attempts to determine the cause for the bounce by parsing the bounce email returned by the receipient's email server. Based on the nature of the bounce and the number of times a bounce has occurred attempting to send to this particular email, a Contact's email may be placed **on hold.**

### Why is a contact's e-mail address put "on hold"?

Here is a list from the database of the different types of bounces, and how many times each bounce must occur before CiviCRM deems **on hold** appropriate.

| id | name | description | `hold_threshold` |
| -- | -- | -- | -- |
| 1 | AOL | AOL Terms of Service complaint | 1 |
| 2 | Away | Recipient is on vacation | 30 |
| 3 | DNS | Unable to resolve recipient domain | 3 |
| 4 | Host | Unable to deliver to destination mail server | 3 |
| 5 | Inactive | User account is no longer active | 1 |
| 6 | Invalid | Email address is not valid | 1 |
| 7 | Loop | Mail routing error | 3 |
| 8 | Quota | User inbox is full | 3 |
| 9 | Relay | Unable to reach destination mail server | 3 |
| 10 | Spam | Message caught by a content filter | 1 |
| 11 | Syntax | Error in SMTP transaction | 3 |


### Removing Hold

1. After running a Search, choose from Actions: **Email - unhold addresses** 

2. You can also remove **on hold** from a single contact by manually editing that contact.

3. Alternatively, you could execute a series of SQL queries aimed at searching for Contacts with **on hold** status and/or batch removing hold status.  Hold status is recorded in the `civicrm_email` table as column `on_hold`. The most basic way of batch removing **on hold** status is by use of a simple query such as this one:

```sql
UPDATE civicrm_email SET on_hold = 0;
```

!!! warning "Warnings"
    * Backup your database before running any direct queries.
    * This query will remove all "ON HOLD" status from email addresses, whether the email should be on hold or NOT!

A more nuanced query might be something like:

```sql
UPDATE civicrm_email ce
LEFT JOIN civicrm_group_contact gc ON ( gc.contact_id = ce.contact_id AND gc.status = 'Added' )
INNER JOIN civicrm_group gr ON ( gr.id = gc.group_id )
SET ce.on_hold = 0
WHERE gr.name = 'give your contact group name';
```
### Using a Google Apps account for the return address

1. Log in to your new return@example.com Google Apps account to check that the new account/password works
    1. Check that the account received the email you sent as an admin.
 (You may wish to [set filters](#filters) at this point.)
1. Google has tightened down secure access to their systems. To get CiviCRM bounce processing to work you need to loosen security for the email account. To do this, make sure you are logged into this bounce email account and go to [https://www.google.com/settings/security/lesssecureapps](https://www.google.com/settings/security/lesssecureapps) and change the setting to **allow less restrictive access** , otherwise you will receive the following error when testing the bounce processing email in CiviCRM: (code: 534, response: 5.7.14 Please log in via your web browser and 5.7.14 then try again. 5.7.14 Learn more at 5.7.14 which means that Gmail blocked access from "a less secure app" (as per [https://support.google.com/accounts/answer/6010255)](https://support.google.com/accounts/answer/6010255))

## Autofiling email activities via EmailProcessor

### Intro

The email processor is a function and scheduled job that will attempt to parse emails in an email account (e.g. a POP3/IMAP inbox) and record a copy as an activity in the contact records that correspond to the To:, Cc:, Bcc:, and From: fields. If no matching contact record is found, a new contact will be created and the email filed under that record.

### Setup

1. If you don't already have it enabled, enable CiviMail under Administer >> Configure >> Global Settings >> Enable CiviCRM Components. You don't need to fully configure or use CiviMail functionality, but it must be enabled in your system in order to activate the email autofiling tool. If you are using Drupal, set permissions to give you access to CiviMail.
1. Under Administer CiviCRM >> CiviMail >> Mail Accounts, configure one or more email accounts. In the "Used For" field select Email-to-Activity Processing.
1. Test it by executing the job under Administer >> System Settings >> Scheduled Jobs >> select "Process Incoming Emails" (API: **fetch_activities** ) and "Execute now."
1. The first time you run the script you should see folders created in your email account to designate processed and ignored emails. After testing, visit the contact record you emailed to and confirm the presence of the email activity.
1. Optionally set it up to run regularly via [scheduled jobs](/setup/jobs.md).

!!! note "Important note on bounce processing and processing incoming emails"
    Because the bounce handling and EmailProcessor routines both process emails and then file them under "processed" and "ignored" emails, they cannot be used on the same email account. That is, you cannot use one email account for both bounce processing and email-to-activity processing. You need to set two separate accounts, one for bounce processing, and one for email-to-activity processing.

#### Tip #2

In the typical implementation you will create a single email account that is used solely for the purpose of autofiling as activities. As you interact with constituents using your email client, you may cc or bcc this account in order to have the email processed for the contact. While you may choose to process your normal incoming email accounts using this tool, doing so may unnecessarily clutter your system, as many inbound emails are not important enough to attach to a contact record in this way.

Another possible implementation to consider if your mail server supports IMAP is:

1. Create a mail folder that will be used to indicate to the processor what emails should be processed. Let's call it `civicrm`.
1. When administering your mail accounts in the steps above, in the "Source" field, enter the path to the folder you just created. Note it will typically be `INBOX.civicrm`, not just `civicrm`.
1. If you want an email in your inbox to be autofiled, move it into this folder. If your mail client supports it, make a copy of the email rather than simply move it, as you likely will want the email to remain in your Inbox or be filed in your primary folder structure.
1. The processor will create subfolders `INBOX.civicrm.CiviMail.processed` and `INBOX.civicrm.CiviMail.ignored`, and will move the email into one of those when done.

#### Tip #3

If you are using Thunderbird as your email program, there is a Bounce plugin that allows you to send a copy of the email to another address (in this case the single email address set up in Tip 2) with the headers intact the same as when it was sent to you. This is different from forwarding where you lose the original From, To, and Cc information, and so will allow the email processor to file it correctly.


#### Tip #4

Another possibility if you are having trouble configuring a mail account to work with your specific server, is to use an intermediary. For example you could write a script to use a program like getmail to pull from the account into a local unix maildir, and then configure the CiviMail account to use the maildir protocol.

#### Tip #5

If you’re using Gmail or Email via Google Apps, there is no built-in automatic cc or bcc function, but you can set up an automatic bcc by using a Greasemonkey script on Firefox browsers, or a Tampermonkey script on Google Chrome.

### Using Firefox:

####  Install the Greasemonkey add-on for Firefox[https://addons.mozilla.org/en-US/firefox/addon/greasemonkey/](https://addons.mozilla.org/en-US/firefox/addon/greasemonkey/)

Install the "automatic bcc" Greasmonkey script: [http://userscripts.org/scripts/show/2255](http://userscripts.org/scripts/show/2255)

Restart Firefox.

Login to your Gmail account using the Firefox browser and hit "Compose" for creating a new email. When the compose window opens up, you should see a dialog box from the "automatic bcc" Greasemonkey script that will prompt you to add a destination bcc email address. You may enter more than one email address separated by commas.
As long as you use the Firefox browser and the Greasemonkey add-on is enabled, your emails will be automatically bcc’d to the email address(es) you specified in the dialog box.

You can test that the script is working correctly by expanding the bcc section of your email compose screen. The bcc field should contain your automatic bcc addresses you entered in the dialog.


### Using Google Chrome:

Install the Tampermonkey add-on for Chrome: [https://chrome.google.com/webstore/detail/dhdgffkkebhmkfjojejmpbldmpobfkfo](https://chrome.google.com/webstore/detail/dhdgffkkebhmkfjojejmpbldmpobfkfo)

Install this “automatic bcc” script: [http://userscripts.org/scripts/show/2255](http://userscripts.org/scripts/show/2255)

Restart Google Chrome.

Login to your Gmail account using the Google Chrome browser and hit "Compose" for creating a new email. When the Compose window opens up, click on “add bcc” and a dialog box from the Tampermonkey script will prompt you to add a destination bcc email address. You may enter more than one email address separated by commas.

As long as you use the Google Chrome browser and the Tampermonkey add-on is enabled, your emails will be automatically bcc’d to the email address(es) you specified in the dialog box.

You can test that the script is working correctly by expanding the bcc section of your email compose screen. The bcc field should contain your automatic bcc addresses you entered in the dialog.
