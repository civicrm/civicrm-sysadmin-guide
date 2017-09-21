# CiviGroup Roles Sync

# CiviGroup Roles Sync

CiviGroup Roles Sync is a module that works with Drupal to keep Drupal Users with a specific role synchronized to the group members of the users' corresponding CiviCRM Contact. Put another way: the module will make sure that users with a certain Drupal role are in a corresponding civicrm group, and vice versa.

CiviGroup Roles Sync will **only work with Static Groups** , **_not Smart Groups_** , unless a contact has been added manually to the smart group.

The first step is to create your group, and add contacts to it. Go to Contacts > Administer Groups and then click the Add New Group button. Create your group.

Then go to Drupal, enable the CiviGroup Roles Sync module under Admin > Build > Modules

Then go to admin > Configure > CiviGroup Role Sync

Click the tab to add new rule

Select the Drupal Role and the CiviCRM Group that you want to keep synchronized.

The next time a user who's contact is in the CiviCRM group logs in, they will be granted the role that you have associated with the group. If you remove them from the group, the next time they log in, the role will be removed.

Unlike CiviMember Role Sync which has configureable options for when the synchronization happens, CiviGroup Role Sync only synchronizes on user login. What this means is that if you have really long cookies on your site, and don't close users sessions, a user could stay logged in and continue to have a role in drupal even after you have removed the user from the associate group in CiviCRM.