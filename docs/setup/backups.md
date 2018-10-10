# Backup Data

Your data is priceless. Perform regular data backups and always keep a current copy (in a location other than on the machine on which you run CiviCRM) to guard against data disasters. Hard drive failure is one of the more common problems requiring a backup, but be prepared for more unusual problems as well.

## Database Backup

Your CiviCRM data is primarily stored in the MySQL database you've configured for your site. Before putting a CiviCRM site into production you should ensure that you have an automated procedure in place for generating a regular backup. Backup frequency depends on how often your data changes and how many days worth of data you can "afford to lose".

!!! tip
    The database server and database name are identified in the CIVICRM_DSN setting in civicrm.settings.php:

    ```
    define( 'CIVICRM_DSN', 'mysql://db_user:user_passord@db_server/db_name?new_link=true' );
    ```
    
If you use a hosting provider, they may be able to offer regular backups as part of your service. It is advisable to inquire as to the disaster contingency plans in place (redundancy, offsite / multiple location storage of backups, etc.).

If you are setting up the backups yourself, there are a number of tool such as **mysqldump** or **phpMyAdmin** which can be used. Searching on _"mysql backup"_ will give you options and links to procedures.

## File-system Backup

In order to restore a complete copy of your site, you may also need to do regular backups of certain directories from your server's file system. These special directories are all configured under **Administer CiviCRM** » **System Settings** » **Directories**.

* If you use **custom fields to attach files to your contacts** (e.g. photos or other images, resumes or other documents, etc.) - then you need regular backups of the **Custom Files Directory**.
* If you have configured **Premiums with images** - then you need to back up the **Images Directory**.
* If you have created **custom template(s)** to replace standard CiviCRM screen(s) - then you need to backup the **Custom Templates Directory**.
* If you have modified core files in the CiviCRM codebase - then you should also have a backup of those files.

One way to test whether your backup is sufficient is to [re-create your CiviCRM database on another machine](/misc/switch-servers.md).

## Security of backups

Having good and regular backups to enable recovery after fire or other disaster, disk failure, or a hack, is part of security. However, hackers know that often a backup is easier to steal than data from the production site. A hacker who steals a database backup is in almost the same position as a hacker who steals a copy of a live database. Whilst having a copy of the the code stolen is usually less serious than having a database backup stolen, if a stolen code backup includes cache files, or your settings files (which include database passwords), they may be useful to a hacker. Sometimes code will include other hard-coded credentials, for example for third-party services, which is risky. It follows that it is important to store backups in secure locations; and that generally it is best practice not to include settings files and cache files in the backup, and not to hard-code any credentials when writing code. Where a backup contains sensitive or private data, consider encrypting the entire backup, or at least the sensitive part of the backup--probably the database dump--so that a hacker who steals it will be unable to read it unless they can crack the encryption password.
