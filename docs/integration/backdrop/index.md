# Integrating with Backdrop

Of all the four CMS that integrate with CiviCRM, Backdrop is most comparable
to the deep integration that can be achieved with Drupal. This is primarily
due to the fact that Backdrop is a port of Drupal 7 and repurposing work done
for Drupal is relatively easy.

When thinking about website integration with Backdrop, it is important to
understand the concept of **Backdrop modules**. It might help you to
think of a module as an application which provides a certain bit of
functionality. For example the *calendar* module allows you to display
calendars on your website. The Backdrop distribution of CiviCRM comes with a
number of Backdrop modules which give you the ability to integrate CiviCRM with
Backdrop in various different ways.


## Backdrop Roles and permissions

When you create a new Backdrop site **User** and CiviCRM is installed a
corresponding CiviCRM **Contact** is also created. Both users and
contacts have a separate identification or index number.

*See also the permissions and access control chapter in initial set up
for a general discussion of access control.*

Your Backdrop website allows you to create a series of **Roles.** Your
website administrator give roles to site users and grant each role
the **permission** to perform a certain series of tasks (view, edit,
delete, and administer contacts) or access a certain set of information
(CiviCRM Contacts, CiviMail, CiviEvents, Backdrop Users, specific content
types, etc.)

Backdrop permission settings allow you to decide what tasks a
specific **Role** can perform or what information a certain **Role** has
access to. Backdrop permissions determine how much access a certain type
of user has to configure modules, view, create, edit, or change types of
content, users, or CiviCRM Contact information.

For more information on Backdrop Roles and Permissions, please read the
documentation on Backdrop Roles at BackdropCMS.org:

- [Users: Roles and Permissions](https://backdropcms.org/user-guide/users-roles-and-permissions)

Backdrop roles are global and will override relevant access controls (see
chapter Initial Setup> Permissions and Access Control) setup in
CiviCRM. For example you can setup Access Control in CiviCRM to restrict
access to contacts but if the Backdrop permission 'Access All Contacts' is
set anyone with that role will be able to see all contacts regardless.

## Backdrop Taxonomies, Content Access, or other Role-based User Access Modules

There are several Backdrop Modules that determine what
Backdrop **Roles** have access to which types of content in your Backdrop
website. This can be based on Backdrop taxonomies, by selecting access to
specific nodes, settings on a specific views, pages, or panels, or based
on certain contexts or rules. A few of the most common modules used for
controlling access to content or include options for controlling content
based on Backdrop **Roles** are:

- Views (included in Backdrop by default)
- [Content View Access](https://backdropcms.org/project/content_view_access)
- [Simple Access Grants](https://backdropcms.org/project/simple_access_grants)

## CiviGroup Roles Sync

The CiviGroup Roles Sync module allows administrators of Backdrop Websites
to streamline the user experience for donors and staff. The CiviGroup
Roles Sync module allows you to:

-   Allow board members, committees, staff, and volunteers to access
    specific content or perform specific tasks.
-   Provide customized experiences for users of specific groups when
    they access your website. For example, when a major donor logs into
    the website display a letter from the President thanking the donor
    for his contribution and participation, or provide a major donors
    with pre-registration access to upcoming events and exclusive news
    or resources.

You should spend some time assessing the different ways that different
sorts of users interact with your website, being sure to ask yourself
whether or not the user experience is dependent on specific organized
groups, teams, committees, or task forces or on whether or not a contact
meets a specific set of criteria, or if you should apply roles to users
manually.

In essence, it allows you to synchronize Backdrop users that have a
certain role with CiviCRM contacts in a certain group.

### Preparation

Before using CiviGroup Roles Sync module you should have the following
prepared:

-   your organization's Groups should be created.
-   the roles you will need for each use case should be created in
    Backdrop.
-   if you are using additional modules to fine-tune access, you should
    have all modules you need installed and configured

When determining what Groups and what Backdrop Roles you should create it
is important to remember that multiple Group Types may grant the same
Backdrop Role and each Group can be used to grant multiple Backdrop roles.

You should enable the module from the Backdrop module administration
screen.

### Syncing CiviCRM Groups to Backdrop Roles

-   Navigate to the CiviGroup Role Sync configuration screen.
-   Click on **Add Association Rule.**
-   Under **CiviCRM Group** select the Group that you want a user to
    have in order to be granted a specific Backdrop Role.
-   Under **Backdrop Role** select the Backdrop Role that should be granted.
-   Click on **Add Association Rule** when you are finished configuring
    your new association rule.

You can always edit or delete existing association rules from the same
configuration screen.

The next time a user who's contact is in the CiviCRM group logs in, they will
be granted the role that you have associated with the group. If you remove them
from the group, the next time they log in, the role will be removed.

!!! note
    Unlike CiviMember Role Sync which has configureable options for when the
    synchronization happens, CiviGroup Role Sync only  synchronizes on user
    login. What this means is that if you have really long cookies on your site,
    and don't close users sessions, a user could stay logged in and continue
    to have a role in Backdrop even after you have removed the user from the
    associated group in CiviCRM.

## CiviMember Roles Sync

A common need is to synchronize your CiviCRM members to your Backdrop roles to
allow you to have, among other things, members' only content on your website
that is only accessible to current members defined by the rules and types you
have set up in CiviCRM.

The CiviMember Roles Sync module is now part of the core Backdrop CiviCRM
package and includes this powerful feature. The CiviMember Roles Sync module
allows administrators of Backdrop Websites to streamline the user experience for
organization members. The CiviMember Roles Sync module allows you to:

-   Automatically grant a Backdrop Role to a user based on the type of
    membership he/she has with your organization and whether or not
    he/she is currently in good standing.
-   Allow a user to access member-only content or perform specific
    member-only tasks.
-   Restrict a non-member or expired member user from accessing
    member-only content or performing member-only tasks until he/she
    registers or renews membership with your organization.

### Scenario: Member-only Content

The Retail Baker's Association of America (RBA) connects bakery
suppliers and owners across the country to foster a community that
encourages exchange of business and industry information, networking,
mentoring, and business opportunities, and establish industry standards
through certification, research, and school programs.

Visitors to their website fall into three different user experiences:

-   Non-Members who purchase resources and materials from RBA's online
    boutique. A non-Member must register for a user account to make a
    transaction so that he or she can keep track of purchase history, so
    that RBA can petition him or her for membership in the future.
-   Current members in good standing who have access to member-only
    resources including discussion boards, programs, marketing
    materials, and digital version of RBA publications.
-   Members with expired memberships or former members who need to renew
    need to log into the RBA website to renew their membership, but
    should not have access to member-only resources until their
    membership has been reactivated.

In order to save their staff from having to grant the Backdrop Role
"Active Member" to a user each time he or she renews his or her
membership and then remove the "Active Member" role if that user cancels
his or her membership or allows his or her membership to expire, RBA
uses CiviMember Role Sync to grant the "Active Member" role to any user
who is either a new or current member and any user who's membership has
fallen into the grace period. Now when a user registers or renews his or
her membership he or she will automatically be granted access to RBA's
Member-Only web content.

### What You Need to Know

This section covers what you need to think about before beginning to
work with CiviMember Role Sync.

You should spend some time assessing the different ways that different
sorts of users interact with your website, being sure to ask yourself
whether or not the use case is dependent on your membership structure
or dependent on the organization of committees, teams, and contacts
meeting a specific set of criteria, or if your workflow simply does not
warrant the use of CiviMember Role Sync at all.

### CiviCRM Membership Types

You should have a full understanding of CiviCRM Membership Types and
CiviCRM Membership Statuses before you begin working with the CiviMember
Role Sync module. You can learn more about the CiviMember component by
reviewing the **MEMBERSHIP** chapter.

### Configuring CiviMember Roles Sync

Before using CiviMember Roles Sync module you should have the following
prepared:

-   your organization's Membership Types and Membership Statuses should
    be created and configured in the CiviMember component.
-   the roles you will need for each use case should be created in
    Backdrop.
-   what content each Membership Type should grant permissions to should
    be agreed upon by your staff
-   what tasks each Membership Type should grant permission to should be
    agreed upon by your staff
-   if you are using additional modules to fine-tune access, you should
    have all modules you need installed and configured

When determining what Membership Types and what Backdrop Roles you should
create it is important to remember that multiple Membership Types may
grant the same Backdrop Role and each Membership Type can be used to grant
multiple Backdrop roles.

*For example: The Retail Bakers Association of America has five
different membership types: Corporate Sponsor, Bakery, Individual,
Student, and Retired Bakery. Having any one of the five Membership Types
will grant a user the "Active Member" Backdrop Role that gives access to
the member-only content. In addition to this, users with the Corporate
Sponsor Membership Type should have access to additional exclusive
content. RBA decides calls the Backdrop role used to access this
additional exclusive content "Corporate Member" and user with the
Corporate Sponsor Membership Type will be granted the "Corporate Member"
role in addition to the "Active Member" role.*

#### Enabling CiviMember Roles Sync Module

1.  To enable the CiviMember Roles Sync Module navigate to your list of
installed Backdrop modules.

    -   Go to **Functionality** from the Administration Menu at the
    top of your screen (**/admin/modules**).

1. Find the Module **CiviMember Roles Sync** and check the box to the left
of the module's name.

1. Click on **Save Configuration**.

#### Syncing CiviCRM Membership Types to Backdrop Roles

1. Navigate to the CiviMember Role Sync configuration screen.

    -   Go to **Configuration > CiviCRM > CiviMember Roles Sync**

1. Click on **Add Association Rule.**

1. Under **Select a CiviMember Membership Type** select the Membership Type
that you want a user to have in order to be granted a specific Backdrop
Role.

1. Under **Select a Backdrop Role** select the Backdrop Role that should be
granted.

    *Example: RBA wants any user with a Current Bakery Membership Type to be
granted the "Active Member" role, so the RBA staff creates a new
Association Rule and selects Bakery under Select a CiviMember Membership
Type and selects "Active Member" under Select a Backdrop Role.*

1. Under **Current Status** select the Membership Status that a user
should have to be granted your selected Backdrop Role.

    *Example: RBA wants to grant any user who has a Membership Status of
either New, Current, or Grace access to the member-only website content,
so the RBA staff checks the boxes next to those three Membership
Statuses.*

1. Under **Expired Status** select the Membership Status that will revoke
the Backdrop Role from the user.

    *Example: RBA wants to make sure that any user whose membership expires
or who cancels their membership has their access to the member-only website
content revoked, so the RBA staff checks the boxes next to Expired and
Canceled.*

1. Click on **Add Association Rule** when you are finished configuring your
new association rule.

1. The page will reload, and you should see the message "Your Association
Rule has been added."

1. Repeat all of the above steps to add all necessary association rules for your
organization.

1. Click on the tab **Manually Synchronize**

1. Click on **Synchronize CiviMember Membership Types to Backdrop Roles
Now**. This will put your new CiviMember Role Sync Association Role
immediately into effect.

#### Editing Existing Association Rules

You can always edit or delete existing association rules.

1. To edit or delete an existing Association Rule, Navigate to the
CiviMember Role Sync configuration screen.

    -   Go to **Configuration > CiviCRM > CiviMember Roles Sync**

1. You should now see a list of all existing CiviMember Role Sync
Association Rules. If not, click on **List Association Rule(s)** tab.

    *   Edit an Existing Association Rule
        *   Find the Association Rule you want to make changes to and click
on **edit** for that Association rule.
        *   Make the changes to your Association Rule and when yo are finished click
on **Edit association rule**.
    *   Delete an Association Rule
        *  Find the Association Rule you want to remove and click on **delete** for
that Association Rule.
        *  Your association rule will be deleted. You will not need to confirm
deletion.

#### Configure when Synchronization Should Happen

 You can select several different options for when synchronization
should occur by navigating to the CiviMember Role Sync Configure screen.

-   Go to **Configuration > CiviCRM > CiviMember Roles Sync >
    Configure**

#### Automatic Synchronization Methods:

-   Synchronize whenever someone logs in or logs out: This method will
    trigger synchronization only after a user logs in or out of his or
    her account. You should be careful when using this method because if
    you have long user sessions a user could continue to have access to
    actions or content until he or she finally logs out. (This is the
    default setting)
-   Synchronize when a Backdrop Cron occurs: This method relies on a cron
    to trigger synchronization. 
-   Synchronize when membership is updated: This method will synchronize
    when the user registers for or renews his or her membership and when
    the organization staff updates a user's membership information from
    the back office.
-   Disable Automatic Synchronization: This method relies on an
    organization staff member to manually trigger synchronization. To
    manually trigger synchronization, go to **CiviMember Roles Sync
    settings**, click on the Manually Synchronize tab, and click
    on Synchronize CiviMember Membership Types to Backdrop Roles Now.

Be sure to click **Save Configuration** after making any changes.

!!! tip
    Test this module by creating a test user with a test CiviCRM membership and an association rule for that type. 
    Log in as that user to ensure you have been granted the appropriate role. Then take away the membership for this 
    user in their CiviCRM record, log back in as the test user, and make sure you no longer have that role.
