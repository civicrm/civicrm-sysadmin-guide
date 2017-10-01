# Views3 Integration

## Drupal 7

These instructions assume CiviCRM and Drupal are on separate databases located on the same physical host.

1. Before making any changes, it is _strongly_ advised that you make backups of your mysql databases and settings.php file.
1. If you have different database users for your CiviCRM and Drupal databases, then you need to grant `SELECT` access for the Drupal user to all the CiviCRM tables.
1. Make sure you are logged into your Drupal site as the administrator.
1. Visit `http://yourdomain.com/civicrm/admin/setting/uf?reset=1`
1. You should see a page with output that begins with: `$databases['default']['default']['prefix']= array(`

    ![](https://wiki.civicrm.org/confluence/download/attachments/86213823/views3integration-1.png?version=1&modificationDate=1372586969000&api=v2)

1. Copy this setting output, including the trailing `);`
1. Open `/sites/default/settings.php` for editing from the root directory of the site, typically `public_html` on most servers.

    !!! note
        This assumes you have a single site installation. The location of the settings file might be different depending on whether or not you have multiple sites

1. Find the database connector array. This is where Drupal stores the information regarding the MySQL database and the necessary credentials to connect to it. The array beings with `$databases = array (` then goes on to include the location and MySQL credentials, as well as additional options.
1. Paste your previously-copied settings output here. Be sure to paste it _after_ the trailing `);`

    ![](https://wiki.civicrm.org/confluence/download/attachments/86213823/views3integration-2.png?version=1&modificationDate=1372586969000&api=v2)
 
1. You may need to indicate the default prefix. If you see an error like the one below:

    ![](https://wiki.civicrm.org/confluence/download/attachments/86213823/views3integration-3.png?version=1&modificationDate=1372586969000&api=v2)

    You need to include an additional line, `'default' => '<drupaltableprefix>'`, in your `settings.php` just after `$database ['default']['default']['prefix']= array(`. You can find more information on this error in [this forum post](http://forum.civicrm.org/index.php?topic=20910.0).

    ![](https://wiki.civicrm.org/confluence/download/attachments/86213823/views3integration-4.png?version=1&modificationDate=1372586967000&api=v2)
    
1. Save the file. You might want to clear your site cache at this point.
1. You should now be able to create views based on CiviCRM data.

## Drupal 6

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

Also–if you use a table prefix in Drupal 6 you probably already have a `$db_prefix="dru_";` or similar statement in your `settings.php`. So you will need to remove any existing declaration of `$db_prefix` and replace it with one similar to above.
