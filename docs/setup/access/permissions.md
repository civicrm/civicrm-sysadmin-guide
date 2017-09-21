# CMS Access Control

##### Return to [Access Control](https://wiki.civicrm.org/confluence/display/CRMDOC/Access+Control) Main Page

### Configuring Access Control

!!! tip "Learn More About Drupal Access Control"

    If you are new to Drupal/CiviCRM, you should review 'Managing access control with permissions and user roles' in the Drupal Handbook prior to configuring CiviCRM-specific roles and permissions.


Configure Drupal Permissions, Roles and Access Rules to manage which users can perform actions.

Go to ADMINISTER CIVICRM. ![](https://wiki.civicrm.org/confluence/download/attachments/86213701/ManageAccessControl.jpg?version=1&modificationDate=1372586723000&api=v2)

Under the USERS AND PERMISSIONS Section, click on PERMISSIONS (ACCESS CONTROL)

Click on the **>>Drupal / Joomla / WordPress Access Control** link. You will be taken to the CMS' Access Control section.

### Configure CiviCRM-Related Permissions in Your CMS

!!! note

    Access to CiviCRM modules, contacts, contribution pages, event pages, and profiles is controlled here. If you want to control access to groups of contacts, [profiles](https://wiki.civicrm.org/confluence/display/CRMDOC/Profiles+Admin), or custom data selectively (i.e. some users can access some but not all data), use CiviCRM's built-in [ACLs](https://wiki.civicrm.org/confluence/pages/viewpage.action?pageId=86213706).

    For example, use this function to control whether to give anonymous users access to your online contribution pages so they can make online donations or to a profile so they could see a directory/profile listing. See more examples below.


The CMS Access Control screen lists available CiviCRM Permissions and the Roles that have that permission:
 ![](https://wiki.civicrm.org/confluence/download/attachments/86213701/permissioncombined.jpg?version=1&modificationDate=1372586722000&api=v2)

!!! warning

    If a CiviCRM module (CiviContribute, CiviEvent, etc.) is turned on, you will see additional permission options listed that are specific to that module, such as **edit contributions** or **event participants**.


Examples:

> If there are staff members who you want to allow to view basic contact info, but not contribution info, you would check **view all contacts** and not **access CiviContribute** - then those staff members won't see the **CiviContribute** tab.
>
> If you want to give them permission to view the contact and view the contributions, but not enter or edit contributions, do not check the **edit contributions** permission. Then they can see but not edit or create new contributions, and they won't see **"add a new contribution"** in the contact Contributions tab.
>
> For **"access all custom data"** - if you are using custom data fields (and you don't want to use advanced permissions through ACL's), you would check this box to allow CiviCRM Users access to custom data fields. **If you are using custom fields in a profile linked to User Registraiton - then you must check this box for the Anonymous role.**
>
> If you want to allow anonymous users of your website to download files, check **"access uploaded files"** to allow anyone who visits your site to download files you post on your site.

Based on your workflow (see PLAN), you will need to decide which group(s) are given which PERMISSION(S).
 Example: Some organizations do not want to give Users permission to administer CiviCRM, so they would **not** put a check under the CiviCRM User column in the row labled "administer CiviCRM". Putting a check in this box would give CiviCRM Users PERMISSION to administer CiviCRM.

Links for Permissions, Roles and Access Rules are located in the Drupal Administer section.

For the latest information about configuring Drupal Roles in your version of Drupal, see the Drupal website: [http://www.drupal.org](http://www.drupal.org)

### Configuring Drupal Roles

Roles allow you to fine tune the security and administration of Drupal. A role defines a group of users that have certain privileges as defined in permissions. Examples of roles include: anonymous user, authenticated user, moderator, administrator and so on. In this area you will define the role names of the various roles.

By default, Drupal comes with two user roles:

* Anonymous user: this role is used for users that don't have a user account or that are not authenticated.
* Authenticated user: this role is automatically granted to all logged in users.

Links for Permissions, Roles and Access Rules are located in the Drupal Administer section.

### Configuring Drupal Access Rules

Links for Permissions, Roles and Access Rules are located in the Drupal Administer section.

For information about configuring Drupal Access Rules in your version of Drupal, see the Drupal website: [http://drupal.org/node/22275](http://drupal.org/node/22275)# Default Permissions and Roles

##### Return to [Access Control](https://wiki.civicrm.org/confluence/display/CRMDOC/Access+Control) Main Page

### Default Permissions and Roles

!!! note "Notes on Specific Permissions"

    * **Make online contributions** :
     If you plan to use CiviContribute and want to allow online contributions, enable this permission. Be sure to assign this permission for the "anonymous" role if you want to allow un-authenticated visitors to make contributions.

    * **View event info** and **Register for events** :
     If you plan to use CiviEvent and want to allow online event registration, enable these permissions. Be sure to assign permissions for the "anonymous" role if you want to allow un-authenticated visitors to view and register for events.

    * **Profile listings and forms** :
     If you want to either collect contact information from constituents and/or expose a searchable directory using a profile, you must assign this permission. Be sure to assign this permission for the "anonymous" role if you want to collect contact information from un-authenticated visitors as part of your online contribution pages or during event registration.

    * **Access all custom data** :
     You must enable this permission for any role which you want to view or edit custom data fields. EXAMPLE: If your site uses Profile(s) which include custom fields, make sure the role(s) that need to access these Profiles have this permission. Be sure to assign this permission for the "anonymous" user role if you want to collect information using custom profiles with custom fields.

    * **Access uploaded files** :
     Enable this permission for any role which you want to view images,photos and files attached to CiviCRM records and screens. Be sure to assign this permission for the "anonymous" role if you want visitors to see photos attached to contact records, Personal Campaign Pages, etc.and / or other documents intended for public consumption.

    * **Access Contact Dashboard** :
     You can provide authenticated users with access to a screen where they can review their subscribed groups, contributions, memberships and event registrations (as applicable). Enable this permission for role(s) for which you want to provide this feature. **Do not** enable this for the "anonymous" role.

    * **Create manual**  **batch** :
     Enable this permission for any user along with 'Access to CiviContribute' and 'View Own/All Manual Batches' permissions whom you want to create a financial batch. **Do not** enable this for the "anonymous" role.

    * **Edit own manual batches:**
     Enable this permission for any user along with 'Access to CiviContribute' and 'Create Manual Batch' permissions whom you want to edit his/her own financial batch. **Do not** enable this for the "anonymous" role.

    * **Edit all manual batches:**
     Enable this permission for any user along with 'Access to CiviContribute' and 'Create Manual Batch' permissions whom you will allow to edit any financial batch. **Do not** enable this for the "anonymous" role.

    * **Close own manual batches:**
     Enable this permission to a user along with 'Access to CiviContribute' permission whom you will allow to 'close' or 'export' his/her own created batches.

    * **Close all manual batches:**
     Enable this permission to a user along with 'Access to CiviContribute' permission whom you will allow to 'close' or 'export' any batches.

    * **Reopen own manual batches:**
     Enable this permission to a user along with 'Access to CiviContribute' permission whom you will allow to 'reopen' his/her own closed batches.

    * **Reopen all manual batches:**
     Enable this permission to a user along with 'Access to CiviContribute' permission whom you will allow to 'reopen' any closed batches.

    !!! warning

    Regardless of permissions assigned, the Anonymous role (visitors who are NOT logged in) is not permitted access to the "back-office" functions of CiviCRM. This includes the CiviCRM Home Page dashboard and any of the functions accessed via the standard CiviCRM menus (Find Contacts, Manage Groups, Import, Administer CiviCRM) as well as the built-in Shortcuts block.


 |

#### CiviCRM includes these access control options out of the box (some will not be visible until you enable the relevant component):

| Permission | Roles with this permission can... |
| --- | --- |
| **access CiviCRM** | Access one or more items in the CiviCRM main menu |
| **access CiviContribute** | Record backend contributions (with _edit contributions_) and view all contributions (for visible contacts) |
| **access CiviEvent** | Create events, view all events, and view participant records (for visible contacts) |
| **access CiviGrant** | Create grants (with _edit grants_) and view all grants |
| **access CiviMail** | Create and view all mailings |
| **access CiviMail subscribe/unsubscribe pages** | Subscribe/unsubscribe from mailing list groups |
| **access CiviMember** | Record backend memberships (with _edit memberships_) and view all memberships (for visible contacts) |
| **access CiviPledge** | Record backend pledges (with _edit pledges_) and view all pledges (for visible contacts) |
| **access CiviReport** | View report instances |
| **access Contact Dashboard** | View Contact Dashboard (for themselves and visible contacts) |
| **access Report Criteria** | Allow user to Change Report search Criteria |
| **access all custom data** | View / Edit all custom data fields and groups |
| **access uploaded files** | View / Download files including images and photos |
| **add contacts** | Create a new contact record in CiviCRM |
| **administer CiviCRM** | Perform all tasks in the **Administer CiviCRM** control panel and Import Contacts |
| **skip IDS check** | v4.4 and later. IDS system is bypassed for users with this permission. Prevents false errors for admin users. |
| **administer Multiple Organizations** | Allow user to add organization to Group |
| **administer Reports** | Manage CiviReport Templates |
| **delete activities** | Delete Activities |
| **delete contacts** | Delete Contacts |
| **delete in CiviCase** | Delete Cases |
| **delete in CiviContribute** | Delete Contribtuions |
| **delete in CiviEvent** | Delete Participants |
| **delete in CiviGrant** | Delete Grants |
| **delete in CiviMail** | Delete Mailing |
| **delete in CiviMember** | Delete Memberships |
| **delete in CiviPledge** | Delete Pledges |
| **edit all contacts** | View, Edit and Delete ANY CONTACT in the CiviCRM database;
 Create and Edit relationships, Tags and other info about the contacts |
| **edit contributions** | New / Update Contribution |
| **edit event participants** | New / Update Participants |
| **edit grants** | New / Update Grant |
| **edit groups** | Create New groups, Edit group settings (e.g. group name, visibility...), delete groups |
| **edit memberships** | New / Update Membership |
| **edit pledges** | New / Update Pledges |
| **import contacts** | Import Contact / Activity |
| **make online contributions** | Allow user to Online Contribution / Donation |
| **profile listings and forms** | Access the Profile Search form and Listings |
| **register for events** | Allow event registration |
| **translate CiviCRM** | Allow User to Enable Multilingual |
| **view all activities** | View All Activities |
| **view all contacts** | View ANY CONTACT in the CiviCRM database (no editing allowed), export
 contact info and perform activities such as Send Email, Log Phone Call, etc. |
| **view event info** | Allows users to view event information pages |
| **view event participants** | Allow user to View all participant of Event |

#### Feature access and contact access work in tandem

In the context of component access (e.g. "access CiviContribute"), the term "visible contacts" means that a user can only view records relating to contacts they have the permission to view. A user with "access CiviMember" may have "view all contacts" and therefore be able to view all memberships. On the other hand, if she doesn't have that permission, she might have CiviCRM ACLs granting the ability to view some or all contacts, and then she'd be able to view all memberships. Another user might have "access CiviMember" but neither "view all contacts" nor any ACLs granting him access to view any contacts. In this case, he would not be able to view any memberships.

!!! note "User Access to Profile Listings"

    Users / roles can be granted access to CiviCRM Profile Listings so that they can share selected information WITHOUT granting **access CiviCRM** permission (i.e. without allowing them to access the CiviCRM menus). This is a reasonable configuration choice the **authenticated user** role. If you want anonymous site visitors to view/search Profile Listings, the you would grant this access to the **anonymous user** role. Please consider using "Limit listings to group" in your profile settings to limit the contacts that are exposed via profile listings.


**Task => Permission**

```
New Case => add contacts + access CiviCase + enabled CiviCase
                     (standalone context)

New Contribution => edit contributions + access CiviContribute + enabled CiviContribute
New Membership => edit memberships + access CiviMember + enabled CiviMember
New Pledge => edit pledges + access CiviPledge + enabled CiviPledge
New Participant => edit event participants + access CiviEvent + enabled CiviEvent
New Grant => edit grants + access CiviGrant + enabled CiviGrant

New Group => edit groups
Manage Groups => edit groups + administer CiviCRM
Group Members => view all contacts

Import Contact => access CiviCRM + import contacts
Import Activity => access CiviCRM + import contacts

Import Contribution => edit contributions + access CiviContribute
Import Member => edit memberships + access CiviMember
Import Participant => edit event participants + access CiviEvent

Tabs
Contribution
    Contribution View => access CiviContribute + view all contacts / edit all contacts
    Contribution Edit => access CiviContribute + view all contacts / edit all contacts + edit contributions
    Contribution Delete => access CiviContribute + delete in CiviContribute

Membership
    Membership View => access CiviMember + view all contacts / edit all contacts
    Membership Edit => access CiviMember + view all contacts / edit all contacts + edit memberships
    Membership Delete => access CiviMember + delete in CiviMember

Event
    Participant View => access CiviEvent + view all contacts / edit all contacts
    Participant Edit => access CiviEvent + view all contacts / edit all contacts + edit event participants
    Participant Delete => access CiviEvent + delete in CiviEvent

Pledges
    Pledge View => access CiviPledge + view all contacts / edit all contacts
    Pledge Edit => access CiviPledge + view all contacts / edit all contacts + edit pledges
    Pledge Delete => access CiviPledge + delete in CiviPledge

Mailings
    New Mailing => access CiviMail
    Draft and Unscheduled Mailings => access CiviMail
    Scheduled and Sent Mailings => access CiviMail
	Archived Mailings => access CiviMail
	Headers, Footers, and Automated Messages => access CiviMail + administer CiviCRM
	Message Templates => access CiviMail + administer CiviCRM
	From Email Addresses => access CiviMail + administer CiviCRM
	New SMS => access CiviMail + administer CiviCRM
	Find Mass SMS => access CiviMail + administer CiviCRM

 Reports
    Show Report Instance List => access CiviReport
    Show Report Templates List => administer Reports
    Manage Templates => administer Reports
    Create New Report(Register) => administer Reports
    Change Report Search Criteria => access Report Criteria

Add Contact => add contacts
Find Contact => view all contacts
Edit Contact => edit all contact
```