# Using Drupal Mailhandler to Archive CiviMail

I wanted to be able to archive emails I send via CiviMail.

This was made possible in drupal by doing the following.

1. Install the modules mailhandler [http://drupal.org/project/mailhandler](http://drupal.org/project/mailhandler)
1. Set it up an email on your server for say newsletter@example.com
1. Make a content type in Drupal called Newsletter Archives
    1. Follow the mailhandler module on how to do this so that emails are posted as nodes to this content type
1. Setup mailhandler to check this box per it's instructions
1. Then make a Group in civi called Newsletter2Archive or what ever you want to call it
1. Make ONE member in this group with the email you make in step 2.
1. So now when you send an email and choose this group from the recipient list the email will go there, get taken in from the mailhandlers, added to the drupal node database.
1. The next thing I did is make a view of ALL nodes of this content type and display them at url newsletter_archives
1. Done. Now you have an HTML archive of emails in drupal from CiviMail