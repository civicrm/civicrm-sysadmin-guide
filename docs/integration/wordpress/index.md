# Integrating with WordPress

CiviCRM integrates with WordPress as a plugin starting with WordPress version 3.3.1 (those using earlier versions of WordPress will need to upgrade prior to installing CiviCRM).

CiviCRM public forms such as online contribution pages and event registration pages will be shown to the public in your selected WordPress theme. When logged in to your Control Panel, CiviCRCM will be shown as a link in the left sidebar menu.

## Synchronize WordPress Users to CivCRM Contacts

CiviCRM offers a function to Synchronize Users to Contacts: CiviCRM will check each user record for a contact record. A new contact record will be created for each user where one does not already exist. To perform this function go to **Administer -> Users and Permissions -> Synchronize Users to Contacts**

## Using Shortcodes to Publish CiviCRM Content in WordPress

WordPress shortcodes enable a user to be able to very easily display dynamic content within a WordPress Page or Post with the use of just one line of text. For more information about WordPress shortcodes [check here](http://en.support.wordpress.com/category/shortcodes/). After installing CiviCRM within your WordPress environment, you can immediately take advantage of CiviCRM specific shortcodes that enable you to display content that relates to contribution pages, event pages, profiles, user dashboards, and petitions. Extensions can also add their own shortcodes. The basic shortcode format is `[civicrm compontent=X]` with various options, including the ID of the component you want to display.

### Shortcodes using tinymce editor (WP Versions 4.9.x and prior)

To add a shortcode either create a new post or new page. If you are using the classic editor (as opposed to the new Gutenberg editor) you will now see a CiviCRM button next to Add Media.

![Screenshot demonstrating the "CiviCRM" button after "Add Media"](/img/wordpress_shortcode_insert.png)

After you click on CiviCRM, you will be able to choose the type of content to display. (The options correspond to the types of content in the list above.)

Dependent upon the shortcode that you select you will have different sub options to cater the information to the end user.

### Explanation of available core shortcodes

| Component | Description | Sample shortcodes |
| --- | --- | --- |
| Contribution Pages | A contribution page. Option to show preview or live. | `[civicrm component="contribution" id="x"]`. Preview with `mode="test"` |
| Event Pages | Either a registration or information page for the event. Option to show live or preview. **Information Page:** the Summary Information about the event including the description as well as the map of the location if you have specified in the event settings. In addition there is a button to register for the event. **Registration page:** bypasses the Summary Information and displays the page for an end user to register. | `[civicrm component="event" action="register" id="x"]` or `[civicrm component="event" action="info" id="x"]`. Show preview with `mode="test"` |
| Profiles | A page for displaying a user's profile. This can be one of 4 modes - edit, view, create or search. The default is create. **Create:** A blank form on your website that an end user can fill out to create a new contact record. **View:** Display contact information to logged in users. For instance, the New Individual profile would show the First Name, Last Name, and Email address. **Edit:** Edit an existing contact record. For logged in users it will display their current contact info which they can update. **Search:** Displays search boxes for fields on the profile which have been set to searchable. The results are displayed in a list and are publicly available. This is configurable in each field.| `[civicrm component="profile" mode="y" gid="x"]` The `gid` attribute selects which profile group is displayed. |
| User Dashboards | The current user's dashboard page. There are no additional options. To identify what related information you have setup to be displayed within the user dashboard i.e. Contributions, memberships, etc. navigate to **Administer > Customize Data and Screens > Display Preferences** and select all of the information you would like to have visible to the user. | `[civicrm component="user-dashboard"]` |
| Petitions | A petition form. The only option is to select which petition to display. | `[civicrm component="petition" id="x"]` |

In addition, any of these can specify a hijack option (`hijack="1"`) and they will replace all other content on that page rather than being displayed inline.

Keep in mind that prior to being able to display any CiviCRM content on you public website that record will have already been created within CiviCRM.

### Shortcodes in Gutenberg editor (Available as a plugin for versions 4.9.x and prior)

The Gutenberg editor is slated to be the default editor released with WordPress 5.0. Gutenberg is currently available as a plugin from [WordPress Plugin Repo](https://wordpress.org/plugins/gutenberg) Gutenberg has a Shortcodes block that can be used to insert shortcodes by typing them in. Currently there is no specific CiviCRM block for Gutenberg so there is no interactive way to see the options available for the shortcode. You will need to know the format and look up IDs and options in CiviCRM before creating the shortcode in WordPress.


## Plugin Integrations

There are several plugins that have been developed to improve the integration between CiviCRM and WordPress.

### [CiviCRM Admin Utilities](https://wordpress.org/plugins/civicrm-admin-utilities/)

CiviCRM Admin Utilities modifies CiviCRM’s behaviour in single site and multisite installs. It does a number of useful things:

* Modifies the styling of the CiviCRM menu to fix a number of issues
* Allows you to choose which Post Types the CiviCRM shortcode button appears on
* In WordPress multisite, allows you to hide CiviCRM on sub-sites

[Download](https://wordpress.org/plugins/civicrm-admin-utilities/)

### [CiviCRM WordPress Profile Sync](https://wordpress.org/plugins/civicrm-wp-profile-sync/)

The CiviCRM WordPress Profile Sync plugin keeps the “First Name”, “Last Name”, “Email Address” and “Website” fields of a WordPress (and BuddyPress) user profile in sync with the corresponding fields of a CiviCRM contact. The synchronisation takes place regardless of whether the changes are made in WordPress, BuddyPress or CiviCRM.

[Download](https://wordpress.org/plugins/civicrm-wp-profile-sync/)

### [CiviCRM WordPress Member Sync](https://wordpress.org/plugins/civicrm-wp-member-sync/)

CiviCRM WordPress Member Sync keeps a WordPress user in sync with a CiviCRM membership by granting either a role or capabilities to a WordPress user who has that membership.

This enables you to have, among other things, members-only content on your website that is only accessible to current members as defined by the membership types and status rules that you set up in CiviCRM. This plugin is compatible with both **[Members](https://wordpress.org/plugins/members/)** and **[Groups](https://wordpress.org/plugins/groups/)** for managing members-only content in WordPress. See the Installation section for details.

[Download](https://wordpress.org/plugins/civicrm-wp-member-sync/)

### [Caldera Forms CiviCRM](https://github.com/mecachisenros/caldera-forms-civicrm)

A WordPress plugin that integrates the [Caldera Forms plugin](https://wordpress.org/plugins/caldera-forms/) with CiviCRM.

The Caldera Forms CiviCRM plugin contains a set of form processors that interact with CiviCRM's API to retrieve, create and update data in CiviCRM. With this plugin, you can create responsive forms that expose CiviCRM fields and entities like Activities, Relationships, Tags, Groups and more.
Features

* Add up to 10 Contacts on the same form
* Auto-populate form if the user is logged in
* Define Contact Type: Organization, Individual, Household, and Custom Contact Subtypes
* Map Custom Fields data
* Add Relationships to each contact
* Create Activities on form submission
* Checksum support to auto-populate form with URLs like _example.com/support?cid={contact.contact_id}&{contact.checksum}_

**Requirements**

To use this plugin, the following is needed:

* WordPress
* CiviCRM 4.6 and above
* [Caldera Forms](https://wordpress.org/plugins/caldera-forms/) to be installed

[Download](https://github.com/mecachisenros/caldera-forms-civicrm)

### [Contact Form 7 CiviCRM integration](https://wordpress.org/plugins/contact-form-7-civicrm-integration/)

This plugin adds integration for CiviCRM to contact form 7. With this plugin it is possible to submit a contact for to an external CiviCRM.

[Download](https://wordpress.org/plugins/contact-form-7-civicrm-integration/)

### [CiviCRM Event Organiser](https://github.com/christianwach/civicrm-event-organiser)

A WordPress plugin for syncing Event Organiser plugin Events with CiviCRM Events. The plugin syncs Event Organiser Events, Venues and Event Categories to their corresponding entities in CiviCRM.
Notes

This plugin requires at least WordPress 3.6 and CiviCRM 4.6.

It also requires:

* [Event Organiser](https://wordpress.org/plugins/event-organiser/) version 3.0 or greater
* [Radio Buttons for Taxonomies](http://wordpress.org/plugins/radio-buttons-for-taxonomies/) to ensure only one event type is selected

[Download](https://github.com/christianwach/civicrm-event-organiser)


## Widgets

WordPress Widgets add content and features to your Sidebars. Examples are the default widgets that come with WordPress: for Categories, Tag cloud, Search, etc. Plugins will often add their own widgets.

### [CiviEvent Widget](https://wordpress.org/plugins/civievent-widget/)

You can use the CiviEvent widget to add two types of widgets for upcoming public events from CiviCRM. There’s no limit to the number of widgets you can add of either type. You can include the widgets in the sidebar like normal, or you can include them via shortcodes in the body of your posts.

[Download](https://wordpress.org/plugins/civievent-widget/)

### [CiviCRM Contribution Page Widget](https://wordpress.org/plugins/civicrm-contribution-page-widget/)

CiviCRM contribution pages allow you to generate a “widget” showing the progress toward a goal. This plugin makes it easy to include one or more contribution page “widgets” as actual WordPress widgets on your sidebar.

[Download](https://wordpress.org/plugins/civicrm-contribution-page-widget/)

### BuddyPress

[BuddyPress](https://wordpress.org/plugins/buddypress/) is a suite of components that are common to a typical social network, and allows for great add-on features through WordPress’s extensive plugin system.

#### [BP Groups CiviCRM Sync](https://wordpress.org/plugins/bp-groups-civicrm-sync/)

A port of the Drupal civicrm_og_sync module for WordPress that enables two-way synchronisation between BuddyPress Groups and CiviCRM. It does not rely on any core CiviCRM files, since any required (or adapted) methods are included.

For each BuddyPress group, the plugin will automatically create two CiviCRM groups:

* A “normal” (mailing list) group containing a contact record for each corresponding BuddyPress group member. This group is assigned the same name as the linked BuddyPress group.
* An “ACL” group containing the contact record of the administrators of the corresponding BuddyPress group. This gives BuddyPress group admins the ability to view and edit members of their group in CiviCRM.

When a new user is added to (or joins) a BuddyPress group, they are automatically added to the corresponding CiviCRM group. Likewise, when a contact is added to the “normal” CiviCRM group, they will be added as a member to the corresponding BuddyPress group. If a contact is added to the CiviCRM “ACL” group, they will be added to the BuddyPress group as an administrator.

[Download](https://wordpress.org/plugins/bp-groups-civicrm-sync/)

#### [BP XProfile WordPress User Sync](https://wordpress.org/plugins/bp-xprofile-wp-user-sync/)

The plugin replaces the default BuddyPress xProfile Name field with two fields called (surprisingly) First Name and Last Name. These field values are kept in sync with the corresponding WordPress user profile fields as well as the BuddyPress xProfile Name field itself.

To be used alongside CiviCRM WordPress Profile Sync

[Download](https://wordpress.org/plugins/bp-xprofile-wp-user-sync/)
