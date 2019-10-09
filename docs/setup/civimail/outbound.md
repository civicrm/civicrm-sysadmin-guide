# Outbound Email

## SMTP

Outbound Email (SMTP) - SMTP server, port and authentication settings for outbound email.

SMTP Server - Enter the name of the SMTP server (machine) in the form: smtp.example.com

!!! note

    If you specify an SSL port in the next field, you must prepend an "ssl://" to the host name. TODO: This ought to get added to the CiviCRM book.


SMTP Port - The standard STMP port is 25. Only change this value if your SMTP server is running on a non-standard port and you know which port.

Authentication - Click Yes or No to indicate if your SMTP server requires authentication (user name + password).

SMTP Username - If your SMTP server requires authentication, enter your Username here AND the Password associated with that user in the next field.

!!! tip

    For most environments, the Username will be the **local-part** of a valid email account on your domain. This is the part that precedes the at-sign (@). EXAMPLE: if a valid email account on your domain is **admin@example.com** , enter **admin** as the Username.


SMTP Password - The password associated with the Username entered in the previous field.

!!! Joomla

    For Joomla! installs, you will need to update both the Admin and Front-end copies of civicrm.settings.php.


Click Save to save your action or Cancel to cancel it.

If successful you will see the message "Your changes have been saved."

!!! note

    We followed the port requirement directions on Google Mail Business Apps Page to use 465 BUT hours later we decided to try 25 and it worked. So for some odd reason we had to use a non-secure port.

    Note: Could this be because the ssl:// was left out of the host name field (see above)?# CiviMail Mailer Settings


## Mailer Settings for the optional CiviMail component.

These settings are used to configure mailer properties for the optional CiviMail component. They are NOT used for the built-in "Send Email to Contacts" feature. Refer to the CiviMail Guide for more information.

!!! note "These settings located at:"

    <<yourdomain>>/civicrm/admin/mail?reset=1


**Mailer Batch Limit** - Throttle email delivery by setting the maximum number of emails sent during each CiviMail run (0 = unlimited).

**Mailer Job Size** - If you want to utilize multi-threading enter the size you want your sub jobs to be split into (0 = disables multi-threading and processes mail as one single job - batch limits still apply).

**VERP Separator** - Separator character used when CiviMail generates VERP (variable envelope return path) Mail-From addresses.

Click **Save** to save your action or **Cancel** to cancel it.

If successful you will see the message "Your changes have been saved."

### Tips for determining a Mailer Batch Limit

Assumptions: The scheduled task "Mailings scheduler" is set to run "always" and runs every time cron runs. If not then adjust accordingly.

1. See if your mail server has a limit. For example many gmail accounts have a limit of 500 emails per hour. Therefore if your cron job runs every 10 minutes or 6 times an hour you can only set the Mailer Batch limit to 500/6 = 83.33 or about 80 rounded down.
1. Even if your mail server is not limiting the amount of email you can send or you already have your limit set appropriately your cron job may time out before delivering that many emails due to things like network speed between your server and the mail server or a low php execution time limit. In this case you will find errors in your apache logs about cron timing out. To determine how many emails are actually being delivered each cron run you can download and analyze the table "civicrm_mailing_event_delivered". For example in Excel you can make a pivot table that counts mailing events per minute out of the downloaded data. If this shows that your server is frequently not able to deliver emails matching your current mailer batch limit you may need to lower it to prevent cron timeouts. You may also want to work with your server or mail server providers to determine the cause of the slow delivery.

For example the attached image shows an Excel pivot table/chart that shows a server that is frquently having problems delivering up to it's limit of 70 emails.

![Excel Pivot Table](/img/mailer_batch_limit_check.png)
