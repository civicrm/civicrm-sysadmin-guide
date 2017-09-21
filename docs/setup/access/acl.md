# Manage Access Control Lists (ACLs)

##### Return to [Access Control](https://wiki.civicrm.org/confluence/display/CRMDOC/Access+Control) Main Page.

### Manage Access Control Lists (ACLs) Overview

Manage ACLs provides a way to create and edit the action and object access you allow for roles.

For example, perhaps you have a [profile](https://wiki.civicrm.org/confluence/display/CRMDOC/Profiles+Admin) that contains confidential health information, you may want to restrict access to that profile to only case managers. You would create a group called case managers and add the appropriate contacts to that group. You would then create the role of case manager and give that role permission to view and edit the profile. You would then assign the role you created to the desired group.

### Manage Access Control Lists (ACLs)

Click on the menu item **Administer**  **>>Manage**  **>>Access Control**.

From the Access Control Page, click on **>>Manage ACLs**.

You will see a list of existing ACL's. You can create a NEW **ACL** or **Edit** , **Disable** or **Delete** EXISTING ACLs using the links to the right of each ACL.

#### NEW

To create a NEW ACL, click on the **>>New ACL** link at the bottom of the list of ACLs.

Select the **Operation** to permit

Select the **Permissioning** Options of **Group** , **Custom Group** , **Profile** and **Role**.

!!! note

    You must set up Groups, Custom Groups, Profiles and Roles before you can use them in ACLs


Enter a **Description**.

Here you can also enable/disable the ACL by checking or unchecking the box labeled **Enabled?**

Click **Save** to save the ACL or **Cancel** to cancel.

If successful, you will see the ACL on the list of ACLs.

#### EXISTING

For EXISTING ACLs you can **Edit** , **Disable** or **Delete** each ACL using the links to the right of each ACL.

#### EDIT

Selecting **Edit** will bring you to the Edit ACLs page where you can change the options. Here you can also enable/disable the ACL by checking or unchecking the box labeled **Enabled?**

Click **Save** to save the ACL.

If successful, you will see the ACL on the list of ACLs.

#### DISABLE

Select **Disable** to temporarily disable an existing ACL. You will see the warning:

> "Are you sure you want to disable this ACL?"

Click **OK** to continue or **Cancel** to cancel disabling the gender option.

To re-enable the ACL, simply click on **Enable**.

#### DELETE

Select **Delete** to delete the ACL. You will be given this warning:

> WARNING: Deleting this option will remove this permission from all ACL Roles. Do you want to continue?

Click **Delete** to continue or **Cancel** to cancel the deletion.# Manage ACL Roles

##### Return to [Access Control](https://wiki.civicrm.org/confluence/display/CRMDOC/Access+Control) Main Page.

### Manage ACL Roles Overview

Manage ACL Roles provides a way to create and modify Roles. Roles are analogous to "jobs" or "positions" in your organization. Examples might be "Operations", "Development Team", "Volunteers". Think about the different types of tasks done by staff and constituents in CiviCRM - and create Roles as needed. Once you've created them, you will be [assigning "permissions"](https://wiki.civicrm.org/confluence/pages/viewpage.action?pageId=86213706) to these Roles which allow them to selectively access and modify data.

### Setup ACL Roles

Begin at the **Administer CiviCRM** page. In the **Manage** section, choose **Access Control**.

From the Access Control Page, click on **>>Manage Roles**.

You will see a list of existing roles (defaults are Administrator and Authenticated). You can create a NEW **ACL Roles** or **Edit** , **Disable** or **Delete** EXISTING roles using the links to the right of each role.

#### NEW

To create a NEW ACL Role, click on the **>>New ACL Role** link at the bottom of the list of roles.

Type in the **Label** , **Description** and **Weight** for each role. You can enable/disable the role by checking or unchecking the box labeled **Enabled?**

Click **Save** to save the role.

If successful, you will see the message:

> The Acl Role "Data Entry" has been saved.

Remember that until you associate a role with a group, it will have no effect. Roles can only be associated with static groups at this time.

#### EXISTING

For EXISTING ACL Roles you can **Edit** , **Disable** or **Delete** each role using the links to the right of each role.

#### The Special Roles: 'Everyone' and 'Authenticated'

CiviCRM comes with two special roles "Everyone" and "Authenticated". When creating ACL Rules, select the special role 'Everyone' if you want to grant this permission to ALL users. 'Everyone' includes anonymous (i.e. not logged in) users. Select the special role 'Authenticated' if you want to grant it to any logged in user.

#### EDIT

Selecting **Edit** will bring you to the Edit ACL Roles page where you can change the **Label** , **Description** and **Weight** for each role. Here you can also enable/disable the role by checking or unchecking the box labeled **Enabled?**

Click **Save** to save the role.

If successful, you will see the message:

> The Acl Role "Data Entry" has been saved.
#### DISABLE

Select **Disable** to temporarily disable an existing ACL Role. You will see the warning:

> "Are you sure you want to disable this ACL Role? Users will not be able to select this value when adding or editing ACL Role"

Click **OK** to continue or **Cancel** to cancel disabling the role.

To re-enable the role, simply click on **Enable**.

#### DELETE

Select **Delete** to delete the role. You will be given this warning:

> WARNING: Deleting this option will result in the loss of all Acl Role related records which use the option. This may mean the loss of a substantial amount of data, and the action cannot be undone. Do you want to continue?

Click **Delete** to continue or **Cancel** to cancel the deletion.# Assign Users to Roles

##### Return to [Access Control](https://wiki.civicrm.org/confluence/display/CRMDOC/Access+Control) Main Page.

### Assign Users to Roles Overview

Assign Users to Roles provides a way to assign a role (e.g. a set of permissions) to the users in a group. In other words, the people in the group have permission to do the things that have been granted to that role. Before doing this step, you must first create the group and assign it the "Access Control" group type. You must also create the Role you want to assign to it. However, you can add and remove people from your access control groups at any time.

You can use Access Control Lists (ACLs) to control access to **groups of contacts** in your database. You can also use ACLs to limit access to **Profile screens** , as well as specific **custom fields**.

!!! tip "Example"

    **You want contacts in your Advisory Board GROUP to only be visible to your organization's management team:**

    * Create a regular GROUP called **Advisory Board** and add the appropriate CONTACTS to this GROUP.
    * Create another GROUP called **Managers** and select "Access Control" as the Group Type. Add each of your managers' contact records to this group.
    * Create a **Managers** ROLE (if you don't already have one)
    * Assign the **edit** or **view** OPERATION (the "edit" operation contains the "view" operation, meaning anyone who can edit can view) to the **Managers** ROLE for the Advisory Board GROUP.

    Now only members of your organization's management team that belong to the **Managers** GROUP will be able to edit or view contacts who are in the **Advisory Board** GROUP.


!!! warning "LImitations on Access Control"

    * Access Control Groups must be static groups. **You can not grant an ACL Role to members of a Smart Group.** We hope to remove this limitation at some point using a caching solution.

    * You CAN control access to contacts in a Smart Group using ACL Roles. However, this configuration is likely to cause moderate to severe performance degradation. Setting up access control on multiple Smart Groups may result in fatal errors due to exceeding the maximum allowable number of JOIN statements in MySQL.

    * Currently, there are performance considerations if you assign too many ACLs to a single user - and at some point the maximum allowable number of JOIN statements may be exceeded. Remember that the total number of ACLs affecting a given user is a factor of the ACLs assigned to the Roles assigned to all of the ACL Groups that the user is a member of.

### Assign Users to Roles

Begin at the **Administer CiviCRM** page. In the **Manage** section, choose **Access Control**.

From the Access Control Page, click on **>>Assign Users to Roles**

You will see a list of existing role assignments (default is 'Administrator' assigned to the group 'Administrators'). You can create a NEW **Role Assignment** or **Edit** , **Disable** or **Delete** EXISTING Role Assignments using the links to the right of each role assignment.

#### NEW

To create a NEW role assignment, click on the **>>New Role Assignment** link at the bottom of the list of role assignments.

!!! note

    You must create the role and the group (with group type = Access Control) before assigning a role to a group.


Choose the **ACL Role** and **Assigned To** value for each role assignment. You can enable/disable the role assignment by checking or unchecking the box labeled **Enabled?**

Click **Save** to save the role.

If successful, you will see the new role assignment listed.

#### EXISTING

For EXISTING Roles you can **Edit** , **Disable** or **Delete** each role assignment using the links to the right of each role.

#### EDIT

Selecting **Edit** will bring you to the Edit ACL Role Assignments page where you can change the **ACL Role** and **Assigned To** value for each role assignment. Here you can also enable/disable the role assignment by checking or unchecking the box labeled **Enabled?**

Click **Save** to save the role assignment.

If successful, you will see the new role assignment listed.

#### DISABLE

Select **Disable** to temporarily disable an existing role assignment. You will see the warning:

> "Are you sure you want to disable this ACL Role Assignment?"

Click **OK** to continue or **Cancel** to cancel disabling the role assignment.

To re-enable the role assignment, simply click on **Enable**.

#### DELETE

Select **Delete** to delete the role assignment. You will be given this warning:

> WARNING: Deleting this option will remove this ACL Role Assignment. Do you want to continue?

Click **Delete** to continue or **Cancel** to cancel the deletion.