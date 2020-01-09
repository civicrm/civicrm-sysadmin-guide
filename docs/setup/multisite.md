# CiviCRM Multisite

## What is Multisite / Multi domain

CiviCRM has built in support for multisite access - several separate sites accessing the same CiviCRM database. (this is different to the Drupal concept which means shared codebase). In most cases a site will be defined by a distinct URL

These sites could be:
* Part of a drupal 'domain module' install - hosted within the same database
* in separate databases sharing a user table (if you do this you probably want the multisite permissioning extension or another piece of code to help you keep uf matches in sync)
* separate databases with their own user table
* dissimilar databases - e.g. drupal 7 &  drupal 8 multisites ( this can be a good migration technique.)

CiviCRM has no idea which of these configurations you are using & does not need to know

You can select from different levels of multisite-ness


| Level | impact |
| ---- | ---- |
| Separate domain records but the database is not configured as a multisite | This basically means you specify the domain in your civicrm.settings.php file. CiviCRM is not really aware of this. Where a table is domain specific it will select the rows that relate to that domain. | 
| Separate domain records with the site configured as a multisite | In addition to the above you select the box that defines the site as a multisite and specify a domain group. Contacts created on this site will be added to this group & groups created on this site will be made sub-groups of this group or associated with the group via a shared group organisation. You do not need to make the top level site multisite-aware unless you specifically want contacts on that site to be added to a group (large hierarchical groups can affect performance) |
| Separate domain records with multisite permissioning | By installing the multisite module on top you cause people on each domain who don't have 'view all contacts' permission or 'edit all contacts' to only see contacts in that domain | 

## Multisite v Settings Management

Since 4.1 it has been possible to manage having different urls & other settings per site by over-riding settings (those which are stored in civicrm_settings.php) in the civicrm.settings.php file

Setting overrides can handle

* different urls
* different directories
* different WysiWIG editors
* different search options
Use different domains if you want:
- domain specific membership types
- domain specific payment processors
- domain specific different from addresses from CiviMails
- domain specific link back URLS in CiviMails
- domain specific domain contact details
- domain specific SMS Providers

## Multilevel permissioning behaviour

This permissioning is delivered by the [multisite extension](https://lab.civicrm.org/extensions/multisite). The extension also helps manage a shared user table (even if you turn permissioning off by setting domain group to 0)

Multilevel permissioning  lends itself very well to a head office branch or chapter organisation as the functionality is hierarchical. The users of the Head Office site can see all the users from all the sub-sites. Users logged into Branch site 1 cannot see users from Branch site 2 unless they have 'view all contacts', 'edit all contacts' or  'administer multisite' permissions. Likewise users logged into branch 2 cannot see Branch 1 users unless they have the extra drupal permissioning. The Head Office users can see both.

The mechanism behind it is groups. Each domain has a domain group (with the likely exception of the top level domain). This group has 'associated groups'. The associated groups are either child groups or groups that share the same group organisation. The preferred method is to share a group organisation as there are less performance costs & there is less potential for confusion. A good use-case for a child group is when you want to add another level to your multisite set up. These level 3 sites will be members of their parent site in a hierarchical organisation.

You should avoid setting up smart groups as child groups. Permissions are only based on 'hard-add's to the group. When a person views a smart group that is an associated group they will see the interesection of that group & contacts that have been hard-added to the parent group or an associated group. However, the site will still incur the performance cost of calculating the whole smart group. In addition, if it is a child group the site will incur the performance cost of storing all the contacts of the child group against the parent group. If a user with 'view all contacts' permission views the site group they will see all the contacts of site group and all child groups INCLUDING smart groups - so what they see when they look at this site group will NOT reflect the contacts that are visible from that site. There may be situations where this makes sense - but in general it increases confusion.

It is possible for a group to be a child of more than one group in which case it will be considered to be in both those groups and can be seen from the site of both those groups.

If the multilevel permissioning module is enabled:

* Contacts with 'view all contacts', 'edit all contacts' will continue to see all contacts
* Other contacts with the 'civicrm view all contacts in domain' permission will be able to see all the contacts in the site group they are logged onto, and contacts hard-added to it's child groups and groups linked by group organisations (associated groups).
* If child groups are smart groups then ACL-restricted users will not see smart group members who are not hard-added to the parent or associated groups. It is not recommended to put smart groups as child groups per above
* The silo-ing of data is at the contact level. It is similar to what can be achieve using CiviCRM ACLS.
* If you have access to view a user then you can see all details including contributions, activities.
* If someone is in more than one site group they will be visible from both sites
* Contacts can only see the groups which are children of their site group unless they have 'administer multiple organizations' permission
* ACL permissions to give users access to view a group from another site do not over-ride the limitations of the multisite. If they have permission to view the treasurers group but it is not part of their site they cannot view members in it .

The multisite functionality is limited from the perspective of providing services to multiple organisations that are not closely linked. Take for a site providing fund raising services for multiple charities (ideally with a single sign-on for a giver). Each charity would want to see the contributions person A gave them and not the contributions that person gave to another charity. At this stage this isn't supported. A number of sensitive bits of data such as activities are not site specific

## Components of multisite functionality

| Functionality | Delivered by | Setting | Comments | Issues |
| --- | ---  | --- | --- | --- |
| Various settings, urls can be domain specific, | CiviCRM Core - Settings table | This can be configured by domain or overridden at runtime in the civicrm.settings.php file - the latter is a good approach when only settings are dependent on the domain being accessed as it is simpler | Works well in general.| | 
| payment processors are domain specific | CiviCRM Core | Based on domain_id in payment processor table | Domain_id set in civicrm.settings.php | |
| Domain contact details are domain specific | CiviCRM Core | Based on the contact_id in the civicrm_domain table | | |
| Navigation is domain specific | CiviCRM Core | Domain_id set in civicrm.settings.php | Works well except for WRT upgrades | A couple of upgrade issues are open on this |
| Reports are Domain specific | CiviCRM Core | Domain_id set in civicrm.settings.php | Works well in general | Upgrades are hit & miss on whether they update these correctly | 
| Group Organization field is exposed | CiviCRM Core Multisite functionality | Domain_id set in civicrm.settings.php. `multisite_is_enabled` is set to 1. | Works fine, allows group to be set Group can be linked to multiple orgs in the schema but only single in the UI | | 
| Contact is added to the group associated with the domain they are created on | CiviCRM Core Multisite functionality | Domain_id set in civicrm.settings.php. multisite domain group is specified.  `multisite_is_enabled` is set to 1 | Works well | | 
| Parent ID is compulsory for any group created This is implemented in 2 ways. People with the permission to ‘administer multiple organisations’ can choose the parents  For others the parent will automatically be set to the domain group of the site it is created on | CiviCRM Core Multisite functionality - note that the parent ID OR the organisational contact_id is required in the latest version of the extension | Domain_id set in civicrm.settings.php multisite domain group is specified  `multisite_is_enabled` is set to 1 | There are 3 possible scenarios for these nested groups 1. The child group is a non-smart group and only contains contacts also in the parent group. In this case the nesting will have no effect on the membership of the parent group 1. The child group is a non-smart group and contains contacts (intentionally or otherwise) not in the child group. The parent group is extended to include these contacts (not hard-added). Contacts added to this type of group extend the ACL visibility for those permitted to see this group 3. Child group is a smart group. Child group criteria extend the parent group. However this extended group is restricted by ACLs. Hence smart groups do not extend any ACLs that apply to the parent group (in core or in code) | This group nesting is problematic as it creates a large load in terms of creating the group_contact_cache entries of 2-3 levels of parent groups and resolving the smart group cache. Using the latest version of the extension & using group organisation for linkages will mitigate this. You can still use the hierarchical approach where you want to build up a hierarchical group for other purposes (e.g. mailing) Changes to groups frequently cause time outs & server load as the group cache is rebuilt for the parent group – inclusive of resolving every child smart group. |
| Contacts without ‘view all contacts’ permission but with ‘view all contacts in site’ are allowed to see all contacts in the domain group | CiviCRM multisite extension |  Domain_id set in civicrm.settings.php, `multisite_is_enabled` is set to 1.  multisite domain group is specified. `multisite_acl_is_enabled` is set to 1. | Permission is based on hard-adds to the domain group. WRT the 3 scenarios above 1) The addition of contacts to both parent and child groups is redundant 2) Hard adds to child groups extend the parent group and are useful where this is the intention (e.g. a child group that represents a sub-site. 3) Smart groups. Smart groups are cached in their entirety, both for the smart group and any parents it may have. This creates a lot of work. However, on their way out they are filtered according to the previous 2 items. | From a contact viewing permissioning point of view this suggests that only where #2 applies the group nesting is useful. As an aside the multisite extension currently works on an either or basis with other ACL hooks. Hence a contact can either view all contacts in domain OR have other code based ACLs applied.  Some thought should go into desired behaviour when both are in use |
|Contacts without ‘view all contacts’ permission but with ‘view all contacts in site’ are allowed to see all groups within the site | CiviCRM multisite extension | Domain_id set in civicrm.settings.php. Multisite domain group is specified. `multisite_is_enabled` is set to 1.  `multisite_acl_is_enabled` is set to 1 | This is currently tied with the group nesting & those groups that are children of the site group are visible. However, as seen above the nesting mechanism should only be used for child groups where it is desirable for hard-adds to that group that are not otherwise part of the domain be included in the parent group – ie. it should be used carefully & deliberately. Potential replacement mechanisms are group_organization & group_type. Either of these could be used to designate which domains a group should be visible on & either from a schema point of view support many to one, from a UI group_organization only supports one-to-one. Potentially people with ‘administer multiple organisations’ could create global smart groups & specify which sites they appear on. Those on sub-sites with administer civicrm could specify if the group is available on child sites. | | 
| Shared User Table handling | Multisite extension | Extension enabled | UF_matches are updated where the user name name is the same on multiple domains. The multisite extension will manage this when enabled even when multisite & acls are turned off | |

## Functional Separation in Multisite

| Entity | Is separated | Notes |
| --- | --- | --- |
| Contacts | No	| However, the multilevel 'multisite' extension adds ACLs that do provide segregation  contact related entities- |
| Activities | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel Extension. |
| Address | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. |
| Phone | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. |
| Email | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. |
| Contribution | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. | 
| Pledge | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. |
| Participants | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. |
| Case | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. |
| Relationship | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. |
| Personal Contribution Page | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. |
| Grant | Possibly | see notes with option value | 
| Membership Type	 | Yes | **Options for Membership types** not owned by a given org appear on the membership search page. However the user will not find any members while searching using these options if they don't belong to their organisation. **Contribution form will allow a membership type** belonging to another org to be included. But if someone signs up they are not automatically added to the other org L2 site manager can see memberships from other organisations and 'appears' have permission to change which site a membership type belongs to (although this fails per item above) | 
| Membership | No	 | |
| Membership Status | No | It would be more generally useful for this to be by membership type than by domain per se |
| Payment Processor	| Yes	| |
| Contact type & subtype | No	| |
| Campaign | No | Desirable | |
| Contribution Type | No | (in multi-country models this might be desirable whereas national organisations are likely to think it a bad thing)
| Contribution Page | No | Desirable |
| Contribution Premium | No | Desirable |
| Event | No | Desirable | 
| Custom fields | No | It isn't but tabed Custom Fields for contacts the display of them can be controlled through the multisite extension
| Dashboard | Yes	| |
| Dedupe rules | No | |
| Financial Account | No | |
| Group | Sort of | Group visibility is dictated by the multilevel module if installed. |
| Scheduled jobs | Yes | beware - your mails won't go out if you don't configure for all domains |
| Location Type | No | |
| Domain location | Yes | ie. street address etc for your organisation is per domain |
| Mailing | Yes | These are per domain.  Before CRM-16981 (Civi 4.7), mailings go out with the correct permissions, but are visible to members of other domains. |
| Mail Settings | Yes | pop / smtp / from addresses are per site |
| Import & export mappings | No | Desirable |
| Menu & navigation | Yes | This is fairly problematic as, at least on the upgrade to 4.1, only the main site menu was upgraded |
| Message Templates | No | |
| Logos & headers |	No | Desirable This isn't something currently storable in Civi & to add your logo / header to workflow templates you need to edit all the individual templates but to support multisite we need to look at storing logos & headers so a smarty token can signify them |
| Option groups & option values | Partially | The option value has a domain id. It doesn't seem to be heavily used - ie. I found mail approval, grant_types & from_email_address to be the only ones with domain ids. But this seems like it might be a good angle for events & others - eg. if the event_type_id is for that domain or has no domain then show on the page. |
| Price sets | Partially |The price sets themselves are but not the price fields & values - which makes the idea of having one price set which shows different fields on each site impossible. |
| Profiles | No	| |
| Relationship Type | No | |	 
| Report Instances | Yes | |
| Smart groups | Partially | If multilevel permissioning is installed then permission to view groups in other parts of the hierarchy is restricted by ACLs |
| SMS Provider | Yes | |
| Settings | Yes | The CiviCRM settings table is domain specific |
| Status Preferences | Yes | CiviCRM 4.7+ |
| Survey | No | |
| Tag	| No | |
| Users | Yes | Users are separately matched to civicrm contacts by domain |
 

## Permissioned Relationships for multilevel ACLs
A lighter-weight solution is to use the [Permissioned Relationship extension](https://civicrm.org/extensions/relationship-permissions-acls). This can be used to limit a user to only those contacts that they have a Permissioned Relationship to, or that those contacts in turn have a Permissioned Relationship to, etc. In other words you can daisy-chain these relationships to create multi-level access controls.

Example: User A - has a Permissioned Relationship to Organisation A - and can therefore 'see' Org A, and if Org A has Permissioned Relationship over eg Org B and Org C, and if those Orgs have Permissioned Relationships over eg Persons B1 and B2, and Persons C1 and C2, then User A can access Orgs A, B and C, and Persons B1, B2, C1 and C2. And this can go further down multiple layers.

This deploys very well without the wiring involved in multisite and hence is more approachable for 'beginners' (and for advanced folk who need a lighter weight solution).

Beauty of this solution is that it can scale really easily. And for Drupal sites, together with Webforms and Views can let delegates of Orgs manage their contacts without actually ever needing to set foot on the CiviCRM side.

