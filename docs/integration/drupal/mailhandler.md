# Using Drupal Mailhandler to Archive CiviMail

These steps will help you archive emails that you send via CiviMail.

1. Install the [mailhandler module](http://drupal.org/project/mailhandler).
1. Set up an email on your server, for example `newsletter@example.com`.
1. Make a content type in Drupal called "Newsletter Archives".
    1. Follow the mailhandler module on how to do this so that emails are posted as nodes to this content type.
1. Setup mailhandler to check this box per its instructions.
1. Then make a Group in CiviCRM, for example called "Newsletter2Archive".
1. Make _one_ member in this group with the email address you created for your server above.
1. So now when you send an email and choose this group from the recipient list the email will go there, get taken in from the mailhandlers, added to the drupal node database.
1. The next thing I did is make a view of ALL nodes of this content type and display them at url newsletter_archives
1. Done. Now you have an HTML archive of emails in drupal from CiviMail