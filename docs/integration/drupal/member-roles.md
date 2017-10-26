# CiviMember Roles Sync

A common need is to synchronize your CiviCRM members to your Drupal roles to allow you to have, among other things, members' only content on your website that is only accessible to current members defined by the rules and types you have set up in CiviCRM.

The CiviMember Roles Sync module is now part of the core Drupal CiviCRM package and includes this powerful feature.

## Configuring CiviMember Roles Sync module

Before you get started, be sure to have created all of your membership types and membership status rules for CiviMember as well as the Drupal role(s) you would like to synchronize them with. **Note** : More than one membership type can synchronize with one Drupal role. In other words, you do not need to create a Drupal role for every membership type. For example, you can simply create one Drupal role called "Members" that all CiviCRM membership types sync with.

1. The first step is to enable the module by going to your Drupal site's modules page at `http://example.org/admin/build/modules` It will be listed under the CiviCRM group of modules.
1. Then visit `http://example.org/admin/user/permissions#module-civicrm_member_roles` to give permission to administrators to administer the module.
1. Visit the module's configuration page at `http://example.org/admin/config/civicrm/civicrm_member_roles`
 Click on "Add Association Rule" to create a rule. You will need to create a rule for every CiviCRM membership type you would like to synchronize. For every membership type, you will need to determine the CiviMember states that define the member as "current" thereby granting them the appropriate Drupal role. It is most common to define "New, Current, & Grace" as current. Similarly, select which states represent the "expired" status thereby removing the Drupal role from the user. It is most common to define "Expired, Pending, Cancelled, & Deceased" as the expired status.
1. Return to the configuration page for CiviMember roles Sync (`http://example.org/admin/config/civicrm/civicrm_member_roles`) and click on configure. This setting determines when Drupal will check if the user has a "Current" membership status in CiviCRM. The default and recommended setting is to synchronize whenever a user logs in or out of the site.
1. The last option that is sometimes necessary is to manually synchronize users. Click on the "Manually Synchronize" tab to do so. You will likely use this when you initially configure this module to synchronize your existing users.

Be sure to test this module by creating a test user with a test CiviCRM membership and an association rule for that type. Log in as that user to ensure you have been granted the appropriate role. Then take away the membership for this user in their CiviCRM record, log back in as the test user, and make sure you no longer have that role.

To read how this module can be used to make a CiviCRM profile directory for members only, visit: [https://wiki.civicrm.org/confluence/display/CRMDOC/Linking+Profiles](https://wiki.civicrm.org/confluence/display/CRMDOC/Linking+Profiles)