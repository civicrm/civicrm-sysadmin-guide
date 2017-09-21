# CiviMail Tactics for Staying Off The Black List

This is to share on how to prevent the problem of ending up in these email providers Spam folders. Using CiviCRM contact mail link and Civimail, I have continually ended up in these folders.

I will touch on Yahoo first then come back later to Gmail and Hotmail

**Yahoo**
  1. Add SPF to your TXT Zone file for DNS. To me this meant going to Godaddy and following their very easy to use instructions. I also use Networksolutions for another Domain and they where NO help so I need to figure out what to do with them.

FIRST go here to test [http://espcoalition.org/senderid/](http://espcoalition.org/senderid/) then you will see, most likely you are missing some settings.

1. Then this site will help you find you SPF settings [http://old.openspf.org/wizard.html](http://old.openspf.org/wizard.html)

1. Once done try the test site again and you will see better results. Now you will also see your email go into the Yahoo Inbox.

Yahoo also has a form you can get to get you off the list [http://help.yahoo.com/l/us/yahoo/mail/postmaster/forms_index.html](http://help.yahoo.com/l/us/yahoo/mail/postmaster/forms_index.html)

Let them know what you want and you will get a form like [http://forum.civicrm.org/index.php/topic,1484.msg6480.html#msg6480](http://forum.civicrm.org/index.php/topic,1484.msg6480.html#msg6480)

You will see there the form they sent me and the good advice given.
**GMAIL**

1. Following the above is a good start. This helped overall with my email to Google.
 Coming soon more on this.

**Hotmail**

Hotmail have a Junk eMail Reporting Program (JMRP). When a user click on the "Spam" button in a Hotmail account, the faulty email is forwarded back to you. You can then exclude the email from your lists. This help to stay off the Spam folder.

1. Subscribe to JMRP : [https://support.msn.com/eform.aspx?productKey=edfsjmrpp&ct=eformts](https://support.msn.com/eform.aspx?productKey=edfsjmrpp&ct=eformts) (use "RFC822 Attachment" if asked for a format).
1. Forward complaint email to your Bounce handling address (you can find this address in civicrm/admin/mailSettings?reset=1).
1. Add a entry in the bounce pattern table (for version before v4.4) by executing the following SQL command :

**`INSERT INTO civicrm_mailing_bounce_pattern (bounce_type_id, pattern) VALUES ((select id from civicrm_mailing_bounce_type where name = "Spam"),
                "X-HmXmrOriginalRecipient");`**

This should suspend any reported emails. Be aware this is not an opt-out. If you want an opt-out, you could use a shell script and the API (with drush civicrm-api or via REST) to opt-out the user.

See also :
  * [http://www.microsoft.com/mscorp/safety/content/technologies/senderid/wizard/](http://www.microsoft.com/mscorp/safety/content/technologies/senderid/wizard/)

* [http://mail.live.com/mail/services.aspx](http://mail.live.com/mail/services.aspx)

**Others**

[http://spf.pobox.com](http://spf.pobox.com)
[http://spam.abuse.net/userhelp/howtocomplain.shtml](http://spam.abuse.net/userhelp/howtocomplain.shtml)
[http://www.openspf.org/
](http://www.openspf.org/)DKIM

**Later**

1. Make sure all your mail servers accept mail from: delivery notifications (NDR)
  1. Make sure your DNS is properly set up and that you are complying with the RFC rules regarding service configuration;
  1. Deploy Domain Keys and SPF for outgoing messages;
  1. Make sure that your mail servers has a reverse DNS entry. Some servers will reject mail without a valid reverse entry specified