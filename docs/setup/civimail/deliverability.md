# Tactics for staying off email blacklists

When sending email from CiviMail, it is not unusual to have a signficant percentage of emails bounced as suspected spam, or delivered but marked by the recipient's email program as suspected spam. Usually paid bulk email services achieve better deliverability because they have experise in handling this problem. Blacklist services and spam filters rarely disclose the details of the rules they use to decide what is spam. However, when receiving a bounce report, often the email headers will give some indication of why the email has bounced. For example bounces from Google Mail addresses often mention that the the SPF record is not valid or missing, and others sometimes name a particular blacklist where your domain or IP address has appeared.

!!! note
    A search will provide links to several email blacklist checkers. The results can point to you any lists of suspected spammers
    which have blacklisted your domain or IP address. In many cases, a blacklisting is short-lived and requires no 
    action provided the points above are fixed. Some blacklists provide a form which you can use to approach them directly 
    to ask to be removed from their blacklist.

## Basic steps

1. Make sure all your mail servers accept [bounce messages](https://en.wikipedia.org/wiki/Bounce_message);
1. Having an [SPF](http://www.openspf.org/) record on the DNS for your server which sends for outgoing messages is very valuable, and optimally also deploy [DKIM](http://www.dkim.org/) and [DMARC](https://dmarc.org/);
1. Make sure that your mail server has a reverse DNS [rDNS] entry: for most shared hosting, and for many private servers, only the server or hosting company can set up an rDNS entry, in which case you should raise a ticket requesting this;
1. Make sure your email server complies with the [RFC rules](https://en.wikipedia.org/wiki/Anti-spam_techniques#Strict_enforcement_of_RFC_standards) regarding service configuration;
1. There are various online tools, such as [http://espcoalition.org/senderid/](http://espcoalition.org/senderid/), which you can use to test your email settings.

!!! note
    If your domain name already has an SPF record, make sure that it includes the IP address of your 
    CiviCRM mail server (which might be a different from the host used for the web server or from your mail servers), 
    and if it doesn't, add this IP address.

## Additional tools

Some leading email providers have additional tools or guidance on improving deliverability. A few examples follow.

### Yahoo

Bulk senders can also improve their reputation with Yahoo by completing the form linked from  [the Yahoo FAQ on deliverability](https://help.yahoo.com/kb/postmaster/yahoo-mail-deliverability-faqs-sln24439.html). Some guidance on completing an earlier iteration of Yahoo's form, which may still be useful, can be found at [https://forum.civicrm.org/index.php%3Ftopic=1484.0#msg6479](https://forum.civicrm.org/index.php%3Ftopic=1484.0#msg6479).

### GMail

Google provides several pages of guidance on email deliverability to Google Mail addresses, for example [https://support.google.com/mail/answer/81126](https://support.google.com/mail/answer/81126).

### Outlook Live

MS Outlook Live mail (formerly also called Hotmail) have a Junk Mail Reporting Program (JMRP). When a user click on the "Spam" button in a Hotmail account or moves an item to their junk folder, a report is forwarded back to you. You can then exclude that recipient from your lists. This amy help your emails to maintain a good reputation with Microsoft's spam filter. Subscribe to JMRP by visiting [https://sendersupport.olc.protection.outlook.com/snds/JMRP.aspx](https://sendersupport.olc.protection.outlook.com/snds/JMRP.aspx) (requires a Microsoft login) and completing the form.

Be aware this is not an opt-out. If you want to opt-out contacts who mark your emails as "Spam" or "Junk", you could use a shell script and the API (with drush civicrm-api or via REST) to opt-out contacts who have marked your email as spam.

See also [https://sendersupport.olc.protection.outlook.com/pm/services.aspx](https://sendersupport.olc.protection.outlook.com/pm/services.aspx).

