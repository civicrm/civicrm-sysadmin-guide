# Views Integration

## Drupal Views

[Views](http://drupal.org/project/views) is a powerful Drupal module
that allows you to display website content e.g. a list of latest news
for the home page. CiviCRM integrates with Drupal Views and allows
CiviCRM data to be shown on your website. For instance, if you wanted to
create a page called 'Partner Organizations' and display it publicly you
could:

1. Select criteria for what Contact data will be displayed such as:
 * Organization contacts only
 * That are tagged 'Partner' in CiviCRM
 * That have a membership of status 'New' or 'Current'
2. Choose which data will be shown such as:
  * Organization name
  * State/Province
  * Website
  * Phone number
3. Choose to show the data in either table or paragraph format
4. Allow the public to filter the results themselves by State/Province

This is just a simple example, the possibilities of Drupal Views and
CiviCRM are far reaching.

### Configuration

#### Drupal 7

These instructions assume CiviCRM and Drupal are on separate databases located on the same physical host. If your CiviCRM and Drupal are located within the same database the 12 steps below are not required. 

1. Before making any changes, it is _strongly_ advised that you make backups of your mysql databases and settings.php file.
1. If you have different database users for your CiviCRM and Drupal databases, then you need to grant `SELECT` access for the Drupal user to all the CiviCRM tables.
1. Make sure you are logged into your Drupal site as the administrator.
1. Visit `http://example.org/civicrm/admin/setting/uf?reset=1`
1. You should see a page with output that begins with: `$databases['default']['default']['prefix']= array(`

    ![Screenshot showing the "Drupal Integration" page, with output of settings to be placed into settings.php](/img/views3integration-1.png)

1. Copy this setting output, including the trailing `);`
1. Open the file `/sites/default/settings.php` for editing from the root directory of the site, typically `public_html` on most servers.

    !!! note
        This assumes you have a single site installation. The location of the settings file might be different depending on whether or not you have multiple sites

1. Go to the bottom of the file and paste in your previously-copied settings output here. If the settings.php file already includes an earlier version of the output please remove the earlier version before pasting the new output. 

    ![Screenshot demonstrating the output of database settings](/img/views3integration-2.png)
 
1. You may need to indicate the default prefix. If you see an error like the one below:

    !!! error ""
        PDOException: SQLSTATE[42S02]: Base table or view not found: 1146 Table 'kckruppc_every.semaphore' doesn't exist: SELECT ...

    You need to include an additional line, `'default' => '<drupaltableprefix>'`, in your `settings.php` just after `$database ['default']['default']['prefix']= array(`. You can find more information on this error in [this forum post](http://forum.civicrm.org/index.php?topic=20910.0).

    ![Screenshot demonstrating where to add a table prefix in your settings](/img/views3integration-4.png)
    
1. Save the file settings.php. You might want to clear your site cache at this point.
1. You should now be able to create Drupal Views based on CiviCRM data.

 !!! note
        If you add a new set of custom fields to CiviCRM you will need to re-do these steps again. So any new fields in the field set can be accessed by Drupal Views. 

#### Drupal 6

NOTE: For Drupal 6 the variable name in the `settings.php` file is different. Rather than `$databases['default']['default']['prefix']= array(`, for Drupal 6 the relevant array is `$db_prefix`, so the beginning lines of the addition to `settings.php` will look something like this:

```php
$db_prefix = array (
 'default' => 'dru_',
 'civicrm_acl' => '`civicrmdb`.',
 'civicrm_acl_cache' => '`civicrmdb`.',
 'civicrm_acl_contact_cache' => '`civicrmdb`.',
 'civicrm_acl_entity_role' => '`civicrmdb`.',
 'civicrm_action_log' => '`civicrmdb`.',
 'civicrm_action_mapping' => '`civicrmdb`.',
 'civicrm_action_schedule' => '`civicrmdb`.',
 'civicrm_activity' => '`civicrmdb`.',
 'civicrm_activity_assignment' => '`civicrmdb`.',
 'civicrm_activity_target' => '`civicrmdb`.',
```

etc.

That example is for a Drupal set that uses table prefix `dru_` and for which CiviCRM is set up to use a separate table named `civicrmdb`. CiviCRM also uses a table prefix but that is automatically added–do not add it again here.

Also–if you use a table prefix in Drupal 6 you probably already have a `$db_prefix="dru_";` or similar statement in your `settings.php`. So you will need to remove any existing declaration of `$db_prefix` and replace it with one similar to above

!!!note
If you are using Custom
Data sets in CiviCRM, anytime you add a new data **set** (not just a
field) you must repeat this process.

### Creating Views Using CiviCRM Data

Views are located in the **Structure** section of the Drupal
administrative menu. When you create a View, give it a name and select
what kind of data you wish to show.

Generally speaking if your view is focused on Contacts (which most are)
you will select Show: CiviCRM Contacts. If you wish to display details
about other CiviCRM data such as events, relationships, contributions or
activities there are additional options that might provide more data
fields for those types.

![Screenshot demonstrating the "Add new view" page](/img/Views-CiviCRM-Partner-1.png)

After the View is created, edit the fields, filters, display and other
configurations to show the data exactly how you'd prefer. Views does
take some experimentation and/or training to get it right. Feel free to
ask questions on [Stack Exchange](https://civicrm.stackexchange.com/) or [Mattermost](https://chat.civicrm.org)
&mdash; or [hire a
consultant](https://civicrm.org/partners-contributors) if you become stuck.

![Screenshot demonstrating the "Edit view" page](/img/Views-CiviCRM-Partner-3.png)  

This is a sample configuration using Views 3 in a Drupal 7 environment.
Your View will probably be slightly different, but this gives you some
examples to get started with.

### Other Things Views and CiviCRM Can Do

-   Lists of upcoming events
-   Building reports on contributions, activities or event registrants
-   Honor roll listing of recent donors
-   Staff, board of directors or committee lists
-   A list of current members
-   ...and more!

