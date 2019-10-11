# Setup

## CiviCRM system status

Starting in 4.7, the CiviCRM System Status screen shows you a view of the current status of your CiviCRM installation. It is found at **Administer > Administration Console > System Status**.

![Screen Shot of CiviCRM System Status](/img/system-status.png)

Items in *Red* need to be addressed as soon as possible. An item in red generally indicates something broken that will stop CiviCRM from working normally.

Items in *Orange* should be addressed as soon as you can and may stop you from completing key CiviCRM tasks. 

Items in *Green* are set up correctly and are on the screen for information only. 

### Fixing issues

If you have an issue that needs to be addressed, click the link provided or the question mark icon for additional support.

## Configuring a New Site

After installing a new instance of CiviCRM, site administrators should review the following list of configuration tasks - and implement any that are related to the features and components that will be used by their organization.

!!! tip
    You must be logged in as a user with **administer CiviCRM** permission to complete these tasks. All configuration settings are accessed from the **Administer CiviCRM** page.


### Site configuration

* **Configure** » **Global Settings** » **Localization** » - Language, currency, list of the countries.
* **Configure** » **Domain Information** - Organization name, email address for system-generated emails, organization address.

### Viewing and Editing Contacts

* **Configure** » **Global Settings** » **Site Preferences** - Configure screen and form elements for Viewing Contacts, Editing Contacts, Advanced Search, Contact Dashboard and WYSIWYG Editor.
* **Configure** » **Global Settings** » **Address Settings** - Format addresses in mailing labels, input forms and screen display.
* **Configure** » **Global Settings** » **Mapping and Geocoding** - to display maps
* **Configure** » **Global Settings** » **Miscellaneous** - Contact search behaviors, RECAPTCHA configuration

### Sending Emails (includes contribution receipts and event confirmations)

* **Configure** » **Global Settings** » **Outbound Email (SMTP/Sendmail)**
* **Option Lists** » **From Email Addresses** - Define general email address(es) that can be used as the FROM address when sending email to contacts from within CiviCRM (e.g. info@example.org)

### Online Contributions / Online Membership Signup / Online Event Registration

* **Configure** » **Global Settings** » **Payment Processors**
* You will also need to change the access rights so anonymous users can contribute/sign up/register

### Organize your contacts

* **Configure** » [Option Lists » Tags (Categories)](http://drupal.demo.civicrm.org/civicrm/admin/tag?reset=1)
* **Manage groups**

Once you've completed these settings, you can move on to exploring and configuring custom fields, profiles, as well as the various optional components for fundraising and constituent engagement:

* CiviContribute and CiviPledge
* CiviEvent
* CiviMember
* CiviMail
* CiviCase
* CiviGrant