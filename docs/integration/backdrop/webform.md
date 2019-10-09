# Webform CiviCRM Integration

## Introduction

[![Video from CiviTeacher.org](https://drupal.org/files/Webform-CiviCRM-Intro.jpg)](http://civiteacher.com/video/introducing-civicrm-drupal-webform-integration "Video from CiviTeacher.org")

These instructions relate to the [Webform CiviCRM module](https://backdropcms.org/project/webform_civicrm) and assume you are already familiar with CiviCRM and the Backdrop [Webform module](https://backdropcms.org/project/webform). Read those manuals first. The video above is from the Drupal version of Webform CiviCRM but everything is substantially similar.

This module enhances Backdrop Webforms to create a smart form builder and processor for CiviCRM. With it you can make user-friendly forms that create and update information about contacts, relationships, cases, activities, event participants, group subscriptions, tags, memberships, contributions, grants, file attachments and custom data.

### Getting Started

* Start with [CiviCRM](http://civicrm.org) installed in Backdrop. Download and enable this module: [Webform](https://backdropcms.org/project/webform). [Instructions on installing modules are here if needed](https://backdropcms.org/user-guide/modules).
* Configure permissions
* Create a new webform (or go to edit an existing one).
* Click on the CiviCRM tab.
* Enable the CRM fields you like, and optionally choose introduction text and other settings.
* Customize the webform settings for your new fields however you wish.

### Features

* Expose contacts and related CiviCRM data to be created or edited via Backdrop Webforms.
* A form can be configured to pre-fill or select existing contacts based on a number of criteria.
* Create or update activities, grants, and cases upon form submission.
* Register contacts for events in a more flexible way than allowed by CiviCRM alone.
* Process memberships and contributions.
* Create relationships and shared addresses between multiple contacts.

### Compared to CiviCRM Profiles

CiviCRM has the ability to embed a "profile" (set of CRM fields) on a page (i.e. user/register) or as a standalone form. This works well in some cases, and not others. Compared to profiles, webforms:

* Are more configurable in display, post-processing, sending emails, etc.
* Can be multi-valued. While profiles can only work with one contact (or one event, or one membership, etc.) at a time, webforms have no such limitations. A webform can register multiple contacts for multiple events _and_ create/renew one or more memberships _and_ create/update one or more activities, _and_ grants, _and_ cases, _and_ relationships etc. all on the same form.
* Store submission results, which can be displayed with Views
* Have advanced features like conditional fields, page breaks, tokens, "save draft," etc.
* Have add-on modules for spam control, layout, and other features.

Conversely, webform_civicrm forms have a few limitations:

* Webforms are not CiviCRM profiles and cannot be used in standard CiviCRM workflows
* Can't be (easily) embedded on the user/register or account pages
* Webforms do not support CiviCRM price-sets or discount codes

### Compared to CiviCRM Contribution Pages

Contribution Pages offer:

* Recurring Payments
* A "Sharing" Block
* Other Amounts in Select options
* Pledges (I haven't used this)
* Tell-a-friend
* Premiums (I haven't used this)
* Progress Widgets
* Personal Campaign Pages

These are not directly supported by Webforms, but there are some workarounds.

* The social "sharing block" can be either recreated as a Backdrop Block, borrow code from the template or use [many social modules](http://bit.ly/1FBAHHg).
* Offering an "Other" amount can be quite useful and [there is a module for that](https://www.backdropcms.org/project/select_or_other).
* The tell-a-friend block can be added to the webform confirmation page using a donation page for "eid": /civicrm/friend?reset=1&eid=1&pcomponent=contribute
* The Progress widget code can be used in a view or block - note if you just need the contribution amount it is returned in json (which needs to be parsed) from: /sites/all/modules/civicrm//extern/widget.php?cpageId=1&widgetId=1&format=3 where "cpageId" is the contribution page ID.
* Similarly, the PCP link is /civicrm/contribute/campaign?action=add&reset=1&pageId=1&component=contribute where pageId is the contribution page ID.

Keep in mind the referenced contribution page must have these features (widget, PCP, etc.) enabled to work properly.

There is a [request in the issue queue to support recurring contributions](https://www.drupal.org/node/2557997).

### Usage Notes

* The webform fields created by this module are ordinary webform fields in almost every way. You can style, rename, nest, or edit them like any other webform field.
* There is no problem mixing CiviCRM and other fields on a webform. Non-CiviCRM fields will be ignored by this module. Pagebreaks and fieldsets are fine too.

## Working with Existing Contacts

![Existing Contact Field](https://drupal.org/files/webform_civicrm4.png)Enabling the "Existing Contact" field gives you many options for how a contact can be autofilled or selected:

* **Widget:** Allows you to determine whether to expose this field to the form as an autocomplete or select element, or hide it and set the value yourself.

* **Default Value:** You can have this contact pre-selected on the form based on the current user, relationships, or other options. This option will be overridden if a valid contact id is supplied in the url (see section on autofilling via url below).

* **Filters:** Limits the list of available choices from which this contact may be autofilled or selected.
**Note:** The "check permissions" filter is enabled by default and will verify that the acting user has permission to view and edit a given contact. In rare cases you will wish to disable this permission check, for example to display a list of schools. You'll generally want to combine doing so with some filters or node-based permissions on the webform. Only disable "check permissions" if you know what you are doing!
**Also note** that, as of CiviCRM 4.2 and later, if you want the 'permissions' check to evaluate contact relationships correctly, you must download and enable the [Related Permissions](https://civicrm.org/extensions/relationship-permissions-acls) CiviCRM extension, which makes permissioned relationships behave like ACLs. This allows, for example, an authenticated user to use the autocomplete widget to search for contacts, but the search will be limited to those contacts for which a permissioned relationship exists in CiviCRM. To enable CiviCRM extensions, refer to the [CiviCRM documentation](/customize/extensions.md).

* **Show/Hide fields:** When an existing contact is selected, you can control which fields the user is allowed to edit and which will be hidden. For example, hiding a contact's name fields will only show them when "Create New Contact" is selected, and makes for a nice select/add combo UX which keeps contacts from getting accidentally renamed.

### Contact References

Some fields contain a CiviCRM contact id, for example activity assignee, case manager, and certain custom fields. This module treats those fields as pointing to a webform contact, which allows you to take advantage of all of the above "Existing Contact" features, but makes setting up the contact reference slightly roundabout. Here are the steps, for example, to assign every activity created by a webform to Bob Smith:

1. From the CiviCRM tab, add a new contact to the form (increment the "Number of Contacts" at the top of the page).
1. Set this new contact to have only one field: Existing Contact
1. Under activity settings, set your activity assignee to be the contact just added.
1. Click save, which takes you back to the Webform tab.
1. Edit the new Existing Contact field, set it to use a static widget (hidden), and set the default value to a specified contact: enter Bob Smith. Disable the "Enforce Permissions" checkbox. Click save.

### Current Employer

Like any contact-reference field, current employer can be configured in a number of ways to suit your needs. Here is a sample configuration that allows users to add/update their employer via an autocomplete:

1. Contact 1 should be of type Individual.
1. Add a second contact to the webform and set it to type "Organization". Check the boxes to expose "Existing Contact" and "Organization Name" for contact 2.
1. Click back to Contact 1 and set "Current Employer" field to Contact 2.
1. Click "Save" on the CiviCRM tab, and you will be returned to the Webform tab.
1. Edit the "Existing Contact" field for Contact 2.
1. Change the "Search Prompt" to something like "- find your employer -" and the "Create Prompt" to something like "- add your employer -"
1. Under "Fields to Hide" select everything (it's just "Name" unless you have exposed other fields for contact 2)
1. Change the "Default Value" to "Relationship to Contact 1" and click "Employer of Contact 1"

Note: The this will only work if the current contact has permission to view organisation contacts; or if the permissions on the "Existing Contact" field for Contact 2 have been ignored by un-checking 'Enforce Permissions' (use with caution).

#### A few variations

* Add some more fields like address and website for contact 2 & optionally set them to be hidden by the existing contact options.
* You can prevent users from creating a new organization by only exposing the "Existing Contact" autocomplete to the form and not the "Organization Name" field. You'll want to change the "Not Found Prompt" to something like "- could not find your employer -"
* This recipe can also work with any other relationship types (spouse, children, etc) with one modification: instead of creating the relationship via the "Current Employer" field, you would do it via the "Relationships" field under contact 2 on the CiviCRM tab.

## Creating new Contacts

The module can also be used to generate new Contact records from information provided in the webform.

Creating Backdrop users at the same time can be achieved using the [Webform Registration](https://github.com/backdrop-contrib/webform_registration) module.

## Autofilling Forms from URL Arguments

This module can autofill and update contacts and activities based on ids supplied in the url. Supported arguments:

* **cid1** =xxx: contact 1's ID; you can also supply cid2 and so on as long as those contacts have an "Existing Contact" field). For backward compatibility, cid works as a substitute for cid1.
* **cs1** =yyy: checksum hash allowing non-privledged users to view a specified contact. Ensure that the cs number matches cid number (i.e. [example.org/my-form?cid2=xxx&cs2=yyy](http://example.org/my-form?cid2=xxx&cs2=yyy)). For backward compatibility, cs works as a substitute for cs1.
* **aid** =zzz: ID of the activity to autofill and update -- specifying an activity from a case works too

### Notes about url arguments:

* The presence of an "Existing Contact" field is _required_ for a contact to be autofilled via url arguments (although the field can be hidden from appearing on the form).
* The contact ID will also be checked against all filters you have set in the "Existing Contact" field.
* If "check permissions" is enabled in the "Existing Contact" field, only contacts the acting user has permission to see will be accepted with two exceptions:
    1. A valid cs value in the url will override permissions.
    1. A logged-in user always has implicit permission for their own contact record.
* Be careful. If "check permissions" is disabled and no filters are set, then a user can enter any contact id in the url and gain access to that contact.
* Activity ID will be ignored if no contact is part of the given activity.
* Setting e.g. cid1=0 will force e.g. contact 1 to _not be autofilled. This argument is appended to the url when the user clicks the "Not You" link._

### Generating URL Arguments

There are several ways to generate a link to a webform with contact ids in the url:

#### From CiviMail

CiviMail provides tokens for adding cid and cs to any link. Your constituents will thank you for not making them fill out their name, address, etc when you already know it. To send out personalized links to your form in CiviMail, simply copy and paste the url provided under "Additional Options" on the CiviCRM tab of your webform into your CiviMail message.
**Note:** CiviMail links will only work for contact 1, so set up your form appropriately so that contact 1 represents the contact to be autofilled. If using webform_civicrm for renewals of membership or anything similar, other contacts can be loaded using instructions found in [Allowing a contact to update their related contacts via Webform-CiviCRM](http://civicrm.stackexchange.com/questions/9195/allowing-a-contact-to-update-their-related-contacts-via-webform-civicrm)

#### From Views

Views tokens make it easy to link to a webform and supply a cid. This works great as a view/edit combo for privledged users, or as a contact form ([example](http://civicrm.org/ambassadors "CiviCRM Ambassadors View")). To do so, create a view of CiviCRM contacts, add a contact_id field (hidden from display) and pick a field to rewrite as a link. The link url would be something like "/myform?cid1=[id]".
 Generating a checksum from views is slightly more complex and requires adding a php snippet to your view output.

#### From Other Webforms

This module provides hidden fields for cid and cs which you can use as tokens, allowing you to send a hashed link from a webform-generated email, or redirect an anonymous user to another webform or CiviCRM page upon submission. An example use for this would be that upon completion of your webform, the contact will receive an email containing a hashed link directing them back to the form in case they wish to update their information. Another example would be to redirect them to a CiviCRM contribution page, pre-filled with their contact information.
 To use this feature, enable the "Contact ID" and "Generate Checksum" fields for a contact, then use those fields' token values in the webform's email or redirect options. Click "edit" on the checksum field for a snippet you can copy and paste.

## Webform Submitting tokens

This module provides 3 tokens that can be used after submitting the webform , these 3 tokens are :

* `[submission:contact-link:?]`
* `[submission:activity-link:?]`
* `[submission:case-link:?]`

Where ? can be replaced with the number of contact, activity or case that the webform is going to create or update starting from 1 (where 1 refers to the first contact/activity or case , 2 refers to the second contact/activity or case ..etc) . Each of these tokens will show a link to the specified contact , activity or case.

E.g ) If your webform is configured to create two contacts and two cases , then you can use all or any of the following tokens inside your post webform submission message :

* `[submission:contact-link:1]`
* `[submission:contact-link:2]`
* `[submission:case-link:1]`
* `[submission:case-link:2]`

And if the result of submitting this webform was creating a contact with ID = 100 and other one with ID = 101 as well as two cases with ID = 50 and ID=51 then the following message should appear after submitting the form :

```
http://example.org/civicrm/contact/view?reset=1&cid=100
http://example.org/civicrm/contact/view?reset=1&cid=101
http://example.org/civicrm/contact/view/case?reset=1&action=view&cid=100&id=50 (assuming the first case was assigned to the first contact)
http://example.org/civicrm/contact/view/case?reset=1&action=view&cid=101&id=51(assuming the first case was assigned to the second contact)
```

Here is how it work in action :

1. configuring the confirmation message :

    ![Animation showing the steps to configure the confirmation message](https://user-images.githubusercontent.com/6275540/28129010-b0400ff4-6729-11e7-9d85-01ad1edc9134.gif)

1. submitting the webform :

    ![Animation showing the steps to submit the webform](https://user-images.githubusercontent.com/6275540/28128507-d24149b2-6727-11e7-9263-690d324d7a0b.gif)

## Option Lists

Any CiviCRM field with options (whether it's a simple yes/no select, your upcoming events, or countries of the world) can be fully customized:

* First create the field on the CiviCRM tab, then visit the Webform tab and click the edit button by that field.
* You can rearrange options by dragging them up and down.
* You can disable options so they don't appear on the form.
* You can set an option to be the default value on the form.
* You can rename options.

The downside of customizable options is that they are static: if you update an option list in CiviCRM, the corresponding webform field will not reflect those changes unless you click the edit button as described above and enable the new options.

**Live Options** : Alternately, you can set a field to use "Live Options." This will load the option list from the CiviCRM database every time the form is viewed. Live option lists cannot be customized, but will ensure your form element stays up-to-date. Because of the slight performance impact, this setting is best used only with options that have some possibility of changing in the future.

## Groups and Tags

This module allows you to tag contacts and add them to groups when they submit the webform. Hold down CTRL or SHIFT to select more than one. Groups/tags you choose on the CiviCRM tab will always be added to the contact, and you can also choose -user select- to make a webform element. See "option lists" above.

**Using a Limited Amount of Groups:** Once you have selected -user select- for the groups in the CiviCRM section of the webform admin section, save and return to the 'components' tab (where all the webform fields are listed). Find your groups field and edit it from the component list. If you change the list from Live to Static, you can then select which of your groups will appear as an option to the user.

**Opt-In Confirmation** : In the "additional options" section is a checkbox to enable confirmation emails when contacts are added to public mailing lists. It is recommended that you leave that option enabled in most situations. You may configure the text of the confirmation message using CiviCRM Automated Messages.

## Custom Data

This module can handle any custom fields you have created for contacts, addresses, event participants, cases, relationships, or activities. Note that not every type of widget exists in the webform module, or behaves exactly the same as its CiviCRM counterpart, for example the "advanced multiselect" becomes a simple "multiselect," "rich text area" becomes a plain text area, and a "datetime" field is split into separate "date" and "time" Webform fields. There are some good add-on modules to give more options, e.g. [Chosen](https://drupal.org/project/chosen), [Webform Autocomplete](https://drupal.org/project/webform_autocomplete), and [Webform Html Textarea](https://drupal.org/project/webform_html_textarea)."

## Event Registration

Event registration via webform is extremely flexible; any number of contacts can be registered for any number of events on a single form. If your form has multiple contacts on it, you may choose to register them each separately for different events, or all together for the same event(s). If you choose to register them together, CiviCRM will show contact 1 as having registered the others.

To allow participants to return to the form and update their registration info later, see the section on sending hashed links from a webform email.

With payment integration, events can be paid for online or offline via webforms (see notes below on supported payment processors). Note that CiviCRM pricesets are not supported and fees should instead be allocated using the "event fee" field provided by this module.

## Editing webform submissions

Editing previous webform submissions will cause the corresponding information in CiviCRM to be updated. This is useful if you want to allow users to edit event bookings, for example. But care should be taken if editing 'old' submissions for a webform: for example if you correct a typing error in an old address submission then the entire submission will be written to the contact record again; as if it had just been submitted.

There is currently a [feature request](https://www.drupal.org/node/2410381) to make the 'Editing webform submissions updates CiviCRM' functionality configurable (enabled/disabled) per webform.

## Memberships

This module can create, renew, or cancel one or more memberships for one or more contacts. Paid memberships can be handled via payment integration (see notes below on supported payment processors). Note that CiviCRM pricesets are not supported and the default "minimum" fee will be charged for each membership term.

## Payments

Using Webforms, you can accept online payments for contributions, events and memberships or any combination of these on a single form.

### Unsupported payment processors

Most major payment processors compatible with CiviCRM should also work with this module. If you are having trouble with the processor of your choice you can [search for an open issue](https://www.drupal.org/project/issues/webform_civicrm?text=payment) on the drupal.org issue tracker or file a new one if you don't find one already open. Note that software development is not free and you will likely need to contribute your own time or funding toward a fix. Development occurs primarily on the Drupal version and is then ported into the Backdrop version of this module.

### Accepting payments through a webform

When creating a contribution, event or membership webform that includes payments, first set up a dedicated contribution page for the sole purpose of embedding it on one or more webforms. Most of the contribution page settings will be ignored by the webform. Only the following need to be configured:

* Title: This will be displayed on the webform
* Financial type
* Currency
* Payment processor
* Email receipt - if enabled, these will be sent from the webform just as they would be from the real contribution page

## State/Province and Country Fields

This module gives similar functionality as core CiviCRM profiles for the state field of an address:

* If you enable both state and country fields for an address, the state list will dynamically update based on the chosen country.
* If you enable a state field but not a country field for an address, only states from your site's default country will be shown.
* If the end-user has scripts disabled, the dynamic state list will degrade to a simple textbox where they may enter the abbreviation.

None of the above applies to custom fields. Custom fields of type state/province will be a non-dynamic dropdown list of all "Available states and provinces" you have enabled in CiviCRM's localization settings. This is the same behavior as on CiviCRM profiles.

## About the _"Not You"_ Message

This feature exists to help prevent a major CRM headache: If users view your form while logged-in as someone else, or they click to your form by following someone else's personalized link (i.e. from a forwarded email), they will see that person's details on the form. Not given any alternative, they are likely to manually clear those fields and type their own information, which would cause the existing contact to be updated with a different person's details, throwing your contact data into confusion.

When enabled, users will see a message instructing them to "click here" if they are not the intended contact. The link will take them to an anonymous version of the form. Make sure unknown users have access to the webform if using this feature. Note that the user will stay logged-in so while webform_civicrm will treat them as as an "unknown" user, they will still have all their usual privledges. (this is the same behavior as if you uncheck the "Autofill Contact 1 with Current User" option on the CiviCRM form settings)

## Cloning a Contact

_This is particularly useful to avoid re-doing all your webform component customizations for each contact on the form._

* Add a contact to the webform via the CiviCRM tab.
* Rename, arrange, and customize all the webform fields for that contact. For now, keep them all within the auto-generated fieldset (although you may add add as many sub-fieldsets within the main one as you like, for example to contain their address fields).
* From the webform tab, click the "Clone contact" button by the contact's fieldset.
* All fields within that fieldset (including non-civicrm fields) will be cloned.
* Note that if there are CiviCRM fields that do not belong to the contact within their fieldset (such as an activity field, or a field belonging to another contact), they will be cloned as well, which would be problematic.

## Retrofitting an Existing Webform

You can start recording CiviCRM contacts even for an existing webform. This falls into two scenarios:

1. You don't have any contact info fields on the form yet (name, address, etc). That's easy, just go to the CiviCRM tab of your webform, check the boxes, and the new fields will be created for you.
1. You already have contact info fields on your form. If people have already been using this form, you might not want to delete those fields because you'd lose their data from all existing submissions. Instead, you can get CiviCRM to start processing those fields by changing their field keys to the ones understood by this module. An easy way to find the correct field key is by going to an existing civicrm-enabled webform (or create a dummy one) and copy the field key you are looking for (or see anatomy of a form key, below). Then visit the CiviCRM tab of your webform and you will see that field is enabled.

### Will Contacts, Activities, etc. be Created Retroactively if I Enable CiviCRM Processing on an Existing Form?

No. That would require some sort of batch update script, which is not part of this module.

## Will Webform Submissions be Updated if a Contact is Modified in CiviCRM?

No. Think of each submission record as a snapshot of what was actually entered on the form. It doesn't necessarily reflect current CRM data.
 But editing an existing webform submission _will_ update the CiviCRM database.

## Changing a Webform Component Type

The Webform module has no method for changing a field from one type to another (i.e. turning a textfield into a select, hidden, etc.). This module fixes that shortcoming for CiviCRM components, since the default widget is not always how you want a field to be presented. On the webform tab, click to edit any CiviCRM component and you will have the option to switch widgets. Note that there are some common sense reasons why some fields would _not_ work well if you changed their type - for example, changing a select into a textfield is probably a bad idea because the user would be likely to enter a value that is not a valid option.

## Anatomy of a Form Key (for geeks only)

This module uses form keys to identify CiviCRM fields. The convenation for these keys contains 6 pieces, connected by underscores. They are:
 civicrm _ number _ entity _ number _ table _ field_key (note that the 6th piece may itself contain underscores)
 For example, the field "civicrm_2_contact_1_address_postal_code" translates to:

* civicrm_ - all civicrm fields start with this
* 2_contact_ - this field belongs to contact 2
* 1_address_ - this field is for this contact's first address
* postal_code - the id of the field (usually the column name in the database)

So this field is for the postal code of the first address of the second contact on the form.

Note that for consistency, all form keys are treated as if everything might be multi-valued. So even though a contact can only have one first_name, the form key for contact 1's first name is still "civicrm_1_contact_1_contact_first_name" which tells us that this is a field for the first contact on the form, and the first (and only) set of contact fields for them.

## Recipes

* [Creating a sophisticated event registration with multiple attendees & conditions](http://civicrm.stackexchange.com/questions/18087/how-to-create-event-registrations-with-single-ticket-vs-table-options-and-creat).

* [Create a case activity form linked from a view of case clients](https://wiki.civicrm.org/confluence/display/CRMDOC/Create+a+case+activity+form+linked+from+a+view+of+case+clients)
