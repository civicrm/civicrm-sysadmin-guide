# Multisites, Multidomain, and Multilevel ACLs

* What is multi-site/ multi-domain?
* Multisite versus Settings Management
* Multilevel permissioning behaviour
* Components of multisite functionality
* Functional Separation in Multisite
* Permissioned Relationships for multilevel ACLs

## What is multi-site/ multi-domain?

CiviCRM has built in support for multisite access - several separate sites accessing the same CiviCRM database. (this is different to the Drupal concept which means shared codebase). In most cases a site will be defined by a distinct URL

These sites could be

* part of a drupal 'domain module' install - hosted within the same database
* in separate databases sharing a user table (if you do this you probably want the multisite permissioning extension or another piece of code to help you keep uf matches in sync)
* separate databases with their own user table
* dissimilar databases - e.g. drupal 6 & drupal 7 multisites ( this can be a good migration technique.)

CiviCRM has no idea which of these configurations you are using & does not need to know

You can select from different levels of multisite-ness

| Level | impact |
| --- | --- |
| Separate domain records but the database is not configured as a multisite | This basically means you specify the domain in your civicrm.settings.php file. CiviCRM is not really aware of this. Where a table is domain specific it will select the rows that relate to that domain. |
| Separate domain records with the site configured as a multisite | In addition to the above you select the box that defines the site as a multisite and specify a domain group. Contacts created on this site will be added to this group & groups created on this site will be made sub-groups of this group or associated with the group via a shared group organisation. You do not need to make the top level site multisite-aware unless you specifically want contacts on that site to be added to a group (large hierarchical groups can affect performance) |
| Separate domain records with multisite permissioning | By installing the multisite module on top you cause people on each domain who don't have 'view all contacts' permission or 'edit all contacts' to only see contacts in that domain |

## Multisite versus Settings Management

Since 4.1 it has been possible to manage having different urls & other settings per site by over-riding settings (those which are stored in civicrm_settings.php) in the civicrm.settings.php file

**Setting overrides can handle**

* different urls
* different directories
* different WysiWIG editors
* different search options

**Use different domains if you want**

* domain specific membership types

* domain specific payment processors

* domain specific different from addresses from CiviMails

* domain specific link back URLS in CiviMails

* domain specific domain contact details

## Multilevel permissioning behaviour

This permissioning is delivered by the [the multisite extension](https://github.com/eileenmcnaughton/org.civicrm.multisite/). **The extension also helps manage a shared user table (even if you turn permissioning off by setting domain group to 0)**

Multilevel permissioning lends itself very well to a head office branch or chapter organisation as the functionality is hierarchical. The users of the Head Office site can see all the users from all the sub-sites. Users logged into Branch site 1 cannot see users from Branch site 2 unless they have 'view all contacts', 'edit all contacts' or 'administer multisite' permissions. Likewise users logged into branch 2 cannot see Branch 1 users unless they have the extra drupal permissioning. The Head Office users can see both.

The mechanism behind it is groups. Each domain has a domain group (with the likely exception of the top level domain). This group has **'associated groups**'. The associated groups are either child groups or groups that share the same group organisation. The preferred method is to share a group organisation as there are less performance costs & there is less potential for confusion. A good use-case for a child group is when you want to add another level to your multisite set up. These level 3 sites will be members of their parent site in a hierarchical organisation.

You should avoid setting up smart groups as child groups. Permissions are only based on 'hard-add's to the group. When a person views a smart group that is an associated group they will see the interesection of that group & contacts that have been hard-added to the parent group or an associated group. However, the site will still incur the performance cost of calculating the whole smart group. In addition, if it is a child group the site will incur the performance cost of storing all the contacts of the child group against the parent group. If a user with 'view all contacts' permission views the site group they will see all the contacts of site group and all child groups INCLUDING smart groups - so what they see when they look at this site group will NOT reflect the contacts that are visible from that site. There may be situations where this makes sense - but in general it increases confusion.

It is possible for a group to be a child of more than one group in which case it will be considered to be in both those groups and can be seen from the site of both those groups.

If the multilevel permissioning module is enabled:

* Contacts with 'view all contacts', 'edit all contacts' will continue to see all contacts
* Other contacts with the 'civicrm view all contacts in domain' permission will be able to see all the contacts in the site group they are logged onto, and contacts hard-added to it's child groups and groups linked by group organisations (associated groups).
* If child groups are smart groups then ACL-restricted users will **not** see smart group members who are not hard-added to the parent or associated groups. It is not recommended to put smart groups as child groups per above
* The silo-ing of data is at the contact level. It is similar to what can be achieve using CiviCRM ACLS.
* If you have access to view a user then you can see all details including contributions, activities.
* If someone is in more than one site group they will be visible from both sites
* Contacts can only see the groups which are children of their site group unless they have 'administer multiple organizations' permission
* ACL permissions to give users access to view a group from another site do not over-ride the limitations of the multisite. If they have permission to view the treasurers group but it is not part of their site they cannot view members in it . The multisite functionality is limited from the perspective of providing services to multiple organisations that are not closely linked. Take for a site providing fund raising services for multiple charities (ideally with a single sign-on for a giver). Each charity would want to see the contributions person A gave them and not the contributions that person gave to another charity. At this stage this isn't supported. A number of sensitive bits of data such as activities are not site specific

## Components of multisite functionality

| **Functionality** | **Delivered by** | **Setting** | **Comments** | **Issues** |
| | | | | |
| Various settings, urls can be domain specific, | CiviCRM Core - Settings table | This can be configured by domain or overridden at runtime in the civicrm.settings.php file - the latter is a good approach when only settings are dependent on the domain being accessed as it is simpler | Works well in general. | |
| payment processors are domain specific | CiviCRM Core | Based on domain_id in payment processor table | Domain_id set in civicrm.settings.php | |
| Domain contact details are domain specific | CiviCRM Core | Based on the contact_id in the civicrm_domain table | | |
| Navigation is domain specific | CiviCRM Core | Domain_id set in civicrm.settings.php | Works well except for WRT upgrades | A couple of upgrade issues are open on this |
| Reports are Domain specific | CiviCRM Core | Domain_id set in civicrm.settings.php | Works well in general | Upgrades are hit & miss on whether they update these correctly |
| Group Organization field is exposed | CiviCRM Core Multisite functionality | Domain_id set in civicrm.settings.php Multisite is_enabled is set to 1 | Works fine, allows group to be set Group can be linked to multiple orgs in the schema but only single in the UI | |
| Contact is added to the group associated with the domain they are created on | CiviCRM Core Multisite functionality | Domain_id set in civicrm.settings.php multisite domain group is specified Multisite is_enabled is set to 1 | Works well in general | Pete has identified that contacts created via current employer mechanism aren’t always added |
| Parent ID is compulsory for any group created This is implemented in 2 ways. People with the permission to ‘administer multiple organisations’ can choose the parents For others the parent will automatically be set to the domain group of the site it is created on | CiviCRM Core Multisite functionality - note that the parent ID OR the organisational contact_id is required in the latest version of the extension | Domain_id set in civicrm.settings.php multisite domain group is specified multisite is_enabled is set to 1 | There are 3 possible scenarios for these nested groups 1) The child group is a non-smart group and only contains contacts also in the parent group. In this case the nesting will have no effect on the membership of the parent group 2) The child group is a non-smart group and contains contacts (intentionally or otherwise) not in the child group. The parent group is extended to include these contacts (not hard-added). Contacts added to this type of group extend the ACL visibility for those permitted to see this group 3) Child group is a smart group. Child group criteria extend the parent group. However this extended group is restricted by ACLs. Hence smart groups do not extend any ACLs that apply to the parent group (in core or in code) | This group nesting is problematic as it creates a large load in terms of creating the group_contact_cache entries of 2-3 levels of parent groups and resolving the smart group cache. Using the latest version of the extension & using group organisation for linkages will mitigate this. You can still use the hierarchical approach where you want to build up a hierarchical group for other purposes (e.g. mailing) Changes to groups frequently cause time outs & server load as the group cache is rebuilt for the parent group – inclusive of resolving every child smart group. |
| Parent group is optional IF group organisation is set | Multisite dev version of extension introduces this [https://github.com/eileenmcnaughton/org.civicrm.multisite](https://github.com/eileenmcnaughton/org.civicrm.multisite) | | | |
| Contacts without ‘view all contacts’ permission but with ‘view all contacts in site’ are allowed to see all contacts in the domain group | CiviCRM multisite extension | Domain_id set in civicrm.settings.php multisite is_enabled is set to 1 multisite domain group is specified ACL_is_enabled is set to 1 | Permission is based on hard-adds to the domain group. WRT the 3 scenarios above 1) The addition of contacts to both parent and child groups is redundant 2) Hard adds to child groups extend the parent group and are useful where this is the intention (e.g. a child group that represents a sub-site. 3) Smart groups. Smart groups are cached in their entirety, both for the smart group and any parents it may have. This creates a lot of work. However, on their way out they are filtered according to the previous 2 items. | From a contact viewing permissioning point of view this suggests that only where #2 applies the group nesting is useful. As an aside - the multisite extension currently works on an either or basis with other ACL hooks. Hence a contact can either view all contacts in domain OR have other code based ACLs applied. Some thought should go into desired behaviour when both are in use |
| **Contacts without ‘view all contacts’ permission but with ‘view all contacts in site’ are allowed to see all groups within the site**
 | CiviCRM multisite extension | Domain_id set in civicrm.settings.php multisite domain group is specified multisite is_enabled is set to 1 ACL_is_enabled is set to 1 | This is currently tied with the group nesting & those groups that are children of the site group are visible. However, as seen above the nesting mechanism should only be used for child groups where it is desirable for hard-adds to that group that are not otherwise part of the domain be included in the parent group – ie. it should be used carefully & deliberately Potential replacement mechanisms are group_organization & group_type. Either of these could be used to designate which domains a group should be visible on & either from a schema point of view support many to one, from a UI group_organization only supports one-to-one Potentially people with ‘administer multiple organisations’ could create global smart groups & specify which sites they appear on. Those on sub-sites with administer civicrm could specify if the group is available on child sites. | See [Proposed changes to multisite](https://wiki.civicrm.org/confluence/display/CRMDOC/Proposed+changes+to+multisite) There is a general problem in that the civicm ACLGroup hook is inconsistently applied in core – see [http://issues.civicrm.org/jira/browse/CRM-12209](http://issues.civicrm.org/jira/browse/CRM-12209) |
| Shared User Table handling | Multisite extension | Extension enabled | UF_matches are updated where the user name name is the same on multiple domains. The multisite extension will manage this when enabled even when multisite & acls are turned off | |

## Functional Separation in Multisite

| Entity | Is separated | Notes |
| --- | --- | --- |
| | | |
| Contacts | No | HOWEVER, the multilevel 'multisite' module adds ACLs that do provide segregation |
| contact related entities- activities address, phone etc contribution pledge participants case personal contribution page relationship | No | The multisite concept only limits access to this data by ACLs - one implementation is the 'multisite' Multilevel module. |
| grant | Possibly | see notes with option value |
| membership type | yes | **Options for Membership types** not owned by a given org appear on the membership search page. However the user will not find any members while searching using these options if they don't belong to their organisation **Contribution form will allow a membership type** belonging to another org to be included. But if someone signs up they are not automatically added to the other org L2 site manager can see memberships from other organisations and 'appears' have permission to change which site a membership type belongs to (although this fails per item above) |
| membership | no |
 |
| membership status | no | it would be more generally useful for this to be by membership type than by domain per se |
| payment processor | yes | |
| | | |
| Contact type & subtype | no | |
| Campaign | no | Desirable |
| Contribution type | no | (in multi-country models this might be desirable whereas national organisations are likely to think it a bad thing) |
| Contribution Page | no | Desirable |
| Contribution Premium | no | Desirable |
| Event | no | Desirable |
| Custom fields | no | |
| Dashboard | yes | |
| Dedupe rules | no | |
| Financial Account | no | |
| Group | sort-of | Group visibility is dictated by the multilevel module if installed. |
| Scheduled jobs | yes | beware - your mails won't go out if you don't configure for all domains |
| Location type | no | |
| Domain location | yes | ie. street address etc for your organisation is per domain |
| Mailing | yes | these are per domain. Before CRM-16981 (Civi 4.7), mailings go out with the correct permissions, but are visible to members of other domains. |
| Mail settings | yes | pop / smtp / from addresses are per site |
| Import & export mappings | no | Desirable |
| Menu & navigation | yes | This is fairly problematic as, at least on the upgrade to 4.1, only the main site menu was upgraded |
| Message templates | no | |
| Logos & headers | no | Desirable This isn't something currently storable in Civi & to add your logo / header to workflow templates you need to edit all the individual templates but to support multisite we need to look at storing logos & headers so a smarty token can signify them |
| Option groups & option values | partially | The option value has a domain id. It doesn't seem to be heavily used - ie. I found mail approval, grant_types & from_email_address to be the only ones with domain ids. But this seems like it might be a good angle for events & others - eg. if the event_type_id is for that domain or has no domain then show on the page. |
| Price sets | partially | The price sets themselves are but not the price fields & values - which makes the idea of having one price set which shows different fields on each site impossible. |
| Profiles | no | |
| Relationship type | no | |
| Report instances | yes | |
| Smart groups | partially | If multilevel permissioning is installed then permission to view groups in other parts of the hierarchy is restricted by ACLs |
| Settings | yes | the CiviCRM settings table is domain specific |
| Status Preferences | yes | CiviCRM 4.7+ |
| Survey | no | |
| Tag | no | |
| Users | yes | Users are separately matched to civicrm contacts by domain |

## Permissioned Relationships for multilevel ACLs

A lighter-weight solution is to use the [Permissioned Relationship](https://civicrm.org/extensions/relationship-permissions-acls) extension. This can be used to limit a user to only those contacts that they have a Permissioned Relationship to, or that those contacts in turn have a Permissioned Relationship to, etc. In other words you can daisy-chain these relationships to create multi-level access controls.

Example: User A - has a Permissioned Relationship to Organisation A - and can therefore 'see' Org A, and if Org A has Permissioned Relationship over eg Org B and Org C, and if those Orgs have Permissioned Relationships over eg Persons B1 and B2, and Persons C1 and C2, then User A can access Orgs A, B and C, and Persons B1, B2, C1 and C2. And this can go further down multiple layers.

This deploys very well without the wiring involved in multisite and hence is more approachable for 'beginners' (and for advanced folk who need a lighter weight solution).

Beauty of this solution is that it can scale really easily afaik. And for Drupal sites, together with Webforms and Views can let delegates of Orgs manage their contacts without actually ever needing to set foot on the civi side.# Multi-level Organization ACL Permissions

## Controlling the Clutter

If your organization needs to create less than 5 or 10 groups with separate permissions, then this page is not for you. Your needs will be better met by creating ACLs in the simpler methods described elsewhere. Those methods don't work well when attempting to manage the permissions of organizations with a large number of subunits, like political parties or NGOs with state chapters like USPIRG. The number of groups becomes unwieldly, with hundreds of groups each needing roles defined for them. What follows is one recipe for how to handle those situations - other based on different approaches including groups and relationships are possible.

## Partitioning Data By Country, Province, Local or Interest-Based Chapters

Many non-profits and political organizations have sub-organizations like district or riding local organizations that do significant fundraising and outreach in their area but not elsewhere. There can also be Provincial or State based levels, where state-wide races or organizing is handled. Similar delegation of responsibility for a geographic region or country occurs with numerous international NGO's. CiviCRM can be set up to allow the staff and volunteers of one of these sub-organizations to View and Edit information about contacts in their area without providing that access to staff and volunteers in other areas. It can also allow staff or volunteers with broader geographic responsibilities to access contacts in all of the local ridings/districts in a province, all of the provinces/states in a country, or all of the regions/countries in the world. There may also be occasions when the divisions are not strict, for example, the activists in a urban area that straddles a provincial border may need ACL permissions to facilitate organizing the contacts in their region which encompasses parts of several ridings and two provinces without granting them access to the whole lists of both provinces.

Similar but non-geographic partitioning can be done based on overlapping communities of interest. For example, an organization may encourage members to join committees focussing on different policy areas, or aspects of their identity like a Women's Committee, an Aboriginal Caucus, or a [LGBTTIQQ2S Commmittee](http://www.pridetoronto.com/about/mission-vision-values/ "Lesbian, Gay, Bisexual, Transsexual, Transgender, Intersex, Queer/Questioning, 2 Spirited"). Leaders, organizers and fundraisers in these commmunities may be given varying levels of permission to contact, view, or edit some or all data regarding the people who join them within the broader organization.

As a result, a contact record could be accessible to their local riding (but no other ridings), their provincial party staff (but not that of other provinces), their national party staff, the organizers of two or three committees they have joined, and an ethnic outreach group because they indicated when joining they do not speak the official languages of the country.

This level of complexity regarding ACL permissions requires a small bit of custom coding as of CiviCRM 3.0. What follows is a simple but flexible recipe for defining the groups of contacts and who has roles with permissions to access them. The exact Roles and their Permissions would be defined using the procedures documented on other pages in this ACL section.

## Example Organization

The NZ Green Party worked with the core CiviCRM team to develop this functionality. In addition to a few people with permissions across their whole national database, they need to create permissions based on their electorates (similar to ridings or districts or constituencies elsewhere), provinces, and branches which might organize in parts of more than one province or electorate.

## Defining Sub-Organizational Units

1. First, we create the three kinds of groups:

    1. Click on Administer >> Customize >> Custom Data.
    1. Click on Create New Group of Custom Fields.
    1. Enter **Regions** (or some other appropriate name) for Group Name.
    1. Select Contacts for Used For.
    1. Save.
    1. Add one or more fields based on the type of organizational unit that needs specific ACL support. If it is possible, it would be good to make at least one of these fields required, to ensure that some Role is made responsible for them. One way to make it easier to do that is to allow a value of 'Not known' for the Electorate and Province. In the NZ Green case, we do the following:

        1. Add an **Electorate** field of type Integer Select.
        1. Add a **Province** field of type Integer Select.
        1. Add a **Branch** field of type Integer Select.
        1. Other types of fields are supported by this recipe, notably String and Radio. But it's likely a bad idea to use a type that is not constrained to a list, like Text. And the sample code used later does not support Multi-Select values, though it could be modified to so if desired, for example if contacts could be simultaneously managed by more than one branch.
1. The data for these fields should be populated, particularly for required fields for existing records.
1. Next, we create the corresponding permission groups:

    1. Click on Administer >> Customize >> Custom Data.
    1. Click on Create New Group of Custom Fields.
    1. Enter **Permissions** (or some other appropriate name) for Group Name.
    1. Select Individuals for Used For (assuming that only individuals are provided with access control, which may not be true).
    1. Save.
    1. Add fields **_with the same name and data type_** as in 1.f. above, though the input type will in most cases need to be changed to Multi-Select in order to allow some CiviCRM users to be permissioned to access contacts in more than one electorate, province, or branch. In our case that means:

        1. Add an **Electorate** field of type Integer Multi-Select.
        1. Add a **Province** field of type Integer Multi-Select.
        1. Add a **Branch** field of type Integer Multi-Select.
1. The next step is to create the custom code that will let CiviCRM decide whether a user's permissions defined in their permissions field allow them to access contacts who are in specific electorate, province and branch Regions.

    1. You will need to put a function into a file that will have be examined for the hook function below. In Drupal installations, the best thing is to put it in a custom module, for example, one containing miscellaneous custom code for your site. (For an introduction to creating and installing a custom Drupal module, see [http://drupal.org/node/231276](http://drupal.org/node/231276).)
    1. TBC

**NB: in CiviCRM 4.2.x:**

* you cannot name two custom fields with the same name (ex: "Electorate" in both Regions and Permissions custom groups). I fixed this by using, for example, "Electorate Permission". Keep in mind that this influences the name of the MySQL field in the database, so the code needs to be updated to reflect this.
* you cannot re-use a multiple-choice option set from another field if they are using different types (ex: "Regions" uses "Integer", but "Permissions" uses Alphanumeric, since multi-select requires an alphanumeric field). Therefore, to avoid using alphanumeric everywhere, you must manage two separate option lists.