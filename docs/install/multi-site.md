# Multi Site Installation

This page explains how to use one CiviCRM database with multiple sites.

## Install the first site

Say `site1.example.com` which would be same as any other normal civicrm installation.

It is not necessary to for the top level site to be multisite aware but if you want it to be then enable multisite by visiting `civicrm/admin/setting/preferences/multisite?reset=1`. If you do this you should also grant the associated permission in Drupal: "CiviCRM: administer Multiple Organizations" to your developers or website administrators.

Create a new contact to be the domain contact for the new domain (note steps 2 & 3 can now be done with the `MultisiteDomain.create` api which is part of the extension)

## Create new database records

You can create the database records in this section automatically if you prefer by installing the [Multisite Permissioning](https://civicrm.org/extensions/multisite-permissioning) extension and using the `MultisiteDomain.create` api as described in the [extension's README.md file](https://github.com/eileenmcnaughton/org.civicrm.multisite/).

### Create the "Default Organization" record
Each CiviCRM domain must have its own [Default Organization](../setup/index.md#edit-domain-information). Create a new Organization record to represent the default organization of the new domain.

### Create the domain record

Insert a new domain record into the CiviCRM database. For example if your new contact is 555

```sql
INSERT INTO `civicrm_domain` (
  `name`,
  `description`,
  `version`,
  contact_id)
SELECT
  'site 2',
  'second test site',
  cd.version,
  555
FROM civicrm_domain cd
WHERE cd.id = 1;
```

### Build the navigation links for new domain

Modify `civicrm_codebase/sql/civicrm_navigation.mysql` file and specify new domain, e.g

```sql
SELECT @domainID := id
FROM civicrm_domain
WHERE `name` = 'Default Domain Name';
```

To

```sql
SELECT @domainID := id
FROM civicrm_domain
WHERE `name` = 'site 2';
```

OR

```sql
SET @domainID = 2;
```

And import this file to your CiviCRM database.

## Create the new site

Setup the CMS for the second site.  In some cases, you will install a new instance of the CMS.  You can also use the existing CMS if you are using the [Domain Access](https://www.drupal.org/project/domain) module for Drupal or the [WordPress multisite network](https://codex.wordpress.org/Create_A_Network) feature.

## Locate the settings file for the second site

Depending on how you would like to install civicrm for site 2, proceed to one of the following steps:

#### Manual CiviCRM installation for site2.example.com.

* Copy civicrm settings file from previous site (site1) to the new site (site2).

    ```
    cp sites/site1.example.com/civicrm.settings.php sites/site2.example.com/
    ```

    Note `sites/site2.example.com/civicrm.settings.php` is your new settings file which needs to be modified.

* The sites can share a single instance of the civicrm code in sites/all/modules/ directory if they are using the same CMS.

* Modify `sites/site2.example.com/civicrm.settings.php` file to adjust settings like `CIVICRM_TEMPLATE_COMPILEDIR`, `CIVICRM_UF_BASEURL` as per new site.
* Enable the CiviCRM module through Drupal, at Site building -> Modules ( `/admin/build/modules` ).

#### Auto + manual installation for site2.example.com

* Use civicrm installer for installing civicrm for `site2.example.com`. Specify a new civicrm db which can be dropped later.
 Note `sites/site2.example.com/civicrm.settings.php` is your new settings file which needs to be modified.

* Adjust `CIVICRM_DSN` setting to use the civicrm db used by site1. Drop the new civicrm db previously created.

* The sites can share a single instance of the civicrm code in `sites/all/modules/` directory.

#### WordPress multisites

If using a WordPress multisite when CiviCRM is activated the `civicrm.settings.php` file is added within the [uploads directory for that site](https://codex.wordpress.org/Multisite_Network_Administration#Uploaded_File_Path). Site 1, generally the main site of the multisite, uses the default upload path `/wp-content/uploads/civicrm`.

If you are looking to share one CiviCRM database across multiple websites, for a chapter like setup, the following needs to be added to your `wp-config.php` file (lives in `public_html/example.com` or `example.com/public_html`, depending on your host).

~~~
// define CiviCRM constants here
if ( ! defined( 'CIVICRM_SETTINGS_PATH' ) ) {
        define( 'CIVICRM_SETTINGS_PATH', '/path/to/wordpress/website/wp-content/uploads/civicrm/civicrm.settings.php' );
}
~~~

This will define the path for all websites on the multisite and when enabled they will access the CiviCRM instance defined by the path settings and access is managed through the `civicrm.settings.php` as this example outlines.

#####Subdomain Wordpress Multisite
If you have your multisite set up as subdomains, follow these steps.

!!! Change your `civicrm.settings.php` file
Keep in mind, you should ONLY change the `civicrm.settings.php` file that lives in `wp-content/uploads/civicrm` (the parent/main site's uploads folder).

Find the block of code that resembles this:
    ```php
    echo "you need to configure site : " . home_url();
    define('CIVICRM_UF_BASEURL' , home_url());
    ```

**Remove it entirely**, and replace it with the following code:
```php
 function civicrm_multisite_get_domain_data() {

	// Define CiviCRM Multisite Domain data.
	$data = array(

		'https://domain.org' => array(
			'domain_id' => 1,
			'domain_group_id' => 7,
			'domain_org_id' => 1,
			//'wp.frontend.base.url' => 'https://domain.org/',
			//'wp.backend.base.url' => 'https://domain.org/wp-admin/',
			'extensionsDir' => '/home/customer/www/domain.org/public_html/extensions',
			'extensionsURL' => 'https://domain.org/extensions',
			'userFrameworkResourceURL' => 'https://domain.org/wp-content/plugins/civicrm/civicrm/',
    ),
    
		'https://sub2.lpnevada.org' => array(
			'domain_id' => 21,
			'domain_group_id' => 13,
			'domain_org_id' => 35603,
			//'wp.frontend.base.url' => 'https://sub1.domain.org/',
			//'wp.backend.base.url' => 'https://sub1.domain.org/wp-admin/',
			'extensionsDir' => '/home/customer/www/domain.org/public_html/extensions',
			'extensionsURL' => 'https://domain.org/extentions',
			'userFrameworkResourceURL' => 'https://domain.org/wp-content/plugins/civicrm/civicrm/',
		),

		'https://sub2.domain.org' => array(
			'domain_id' => 20,
			'domain_group_id' => 23,
			'domain_org_id' => 35604,
			//'wp.frontend.base.url' => 'https://sub2.domain.org/',
			//'wp.backend.base.url' => 'https://sub2.domain.org/wp-admin/',
			'extensionsDir' => '/home/customer/www/lpnevada.org/public_html/extensions',
			'extensionsURL' => 'https://domain.org/extentions',
			'userFrameworkResourceURL' => 'https://domain.org/wp-content/plugins/civicrm/civicrm/',
    ),
    
		'https://sub3.domain.org' => array(
			'domain_id' => 22,
			'domain_group_id' => 24,
			'domain_org_id' => 35605,
			//'wp.frontend.base.url' => 'https://sub3.domain.org/',
			//'wp.backend.base.url' => 'https://sub3.domain.org/wp-admin/',
			'extensionsDir' => '/home/customer/www/domain.org/public_html/extensions',
			'extensionsURL' => 'https://domain.org/extentions',
			'userFrameworkResourceURL' => 'https://domain.org/wp-content/plugins/civicrm/civicrm/',
    ),

	);
```

Rinse and repeat for any additional subdomains.

Big thanks to **haystack** for [this code](https://gist.github.com/christianwach/5ca120670152df3dbfb8d3ca42079a96), which was adapted to fit subdomain setup.

You do NOT need to do the **Create the domain record** step (stated above) if you use [this](https://github.com/eileenmcnaughton/org.civicrm.multisite) extension. It is recommended you copy and paste the following command into your shell/terminal (just updating it a little bit here from the repo's original README.md):

`wp cv api MultisiteDomain.create debug=1 sequential=1 name="Bobita" is_transactional=0 group_id="2" contact_id="4"`

!!! Important Note
`group_id`: This can be found as the `group_id` column in the table called `civicrm_group_organization`. 
`contact_id`: This can be found as the `organization_id` column in the table called `civicrm_group_organization`.

**You will need to do this for each subdomain, so keyboard shortcuts are strongly encouraged.**

After you've done this, if you don't have it already, download and install the [CiviCRM Admin Utilities extension](https://wordpress.org/plugins/civicrm-admin-utilities/) plugin for WordPress. **Make sure it (and CiviCRM itself) is Network Activated!** You will need it in a second.

Go to one of your subdomains via its address, i.e. http://sub1.domain.org. 

Along your left menu, find **Settings**, and hover over it. Click **CiviCRM Admin Utilities** when the menu pops out.

Scroll to the bottom, find where it says **Miscellaneous Utilities**.
1. Check the box next to **Clear Caches**.
2. Once it refreshes the page, return to the CiviCRM Admin Utilities page. Click the link that says **Click this to rebuild the CiviCRM menu.**
3. Again, once it refreshes, return to the CiviCRM Admin Utilities page. Click the last link that says **Click this to rebuild the triggers in the CiviCRM database.**

Once you have that, you should be good to go!

HOWEVER...

If you still experience errors or problems at this point, make sure to clear your browser cache. If to no avail, try private/incognito browsing. Finally, as a last point, clear your DNS cache.

Windows (Terminal/CMD): `ipconfig /flushdns`
Mac/Linux: `sudo killall -HUP mDNSResponder`

Enjoy!

**Recommendation**: Create a Site Admin role for any administrator of sub-sites that are sharing CiviCRM, meaning the default Administrator role is only used on the main site. This is recommended since by default all Adminstrators automatically get access to Administer CiviCRM and will be able to create things like custom data fields. By creating a Site Admin role the CiviCRM permissions can be managed from [Administer > Users and Permissions > Permissions (Access Control)](https://docs.civicrm.org/user/en/latest/initial-set-up/permissions-and-access-control/#access-control-permissions-in-wordpress) and will allow only main site administrator access to Administer CiviCRM.

#### Drupal with the Domain Access module

!!! note
    If you're using Drupal with Domain Access, consider installing the [Domain Access CiviCRM](https://civicrm.org/extensions/domain-access-civicrm) module if users might be creating new Drupal accounts via CiviCRM profiles.

Similar to WordPress, with Domain Access module there is only one copy of the codebase, and one copy of civicrm.settings.php.

The easiest is to assign the same domain_id's in Drupal and CiviCRM, then you can just replace this line:

```php
define( 'CIVICRM_DOMAIN_ID' , 1 );
```

With:

```php
if (!function_exists('domain_get_domain')) {
  // The Domain Access module is not enabled
  define('CIVICRM_DOMAIN_ID', 1);
}
else {
  // Get domain_id from the Drupal setup - Drupal and CiviCRM domain IDs MUST match!
  $domain = domain_get_domain();
  define('CIVICRM_DOMAIN_ID', $domain['domain_id']);
}
```

Otherwise you must replace the above line with a conditional similar to:

```php
//define the per-domain settings here first in case we're running from CLI (e.g. bin/csv/import.php)
if(empty($_SERVER['SERVER_NAME'])) {
  define( 'CIVICRM_DOMAIN_ID', 1 );
  define( 'CIVICRM_UF_BASEURL' , 'http://example.local/' );
}
```

```php
switch ($_SERVER['SERVER_NAME']) {
  case 'example.local':
    define( 'CIVICRM_DOMAIN_ID', 1 );
    define( 'CIVICRM_UF_BASEURL' , 'http://example.local/' );
    break;
  case 'cdp.example.local':
    define( 'CIVICRM_DOMAIN_ID', 2 );
    define( 'CIVICRM_UF_BASEURL' , 'http://cdp.example.local/' );
    break;
  case 'svp.example.local':
    define( 'CIVICRM_DOMAIN_ID', 3 );
    define( 'CIVICRM_UF_BASEURL' , 'http://svp.example.local/' );
    break;
}
```

With either of the above, skip the following "Register a new domain" step.

## Register a new domain

Modify located civicrm.settings.php file (for site2) to change following line -

```php
define( 'CIVICRM_DOMAIN_ID' , 1 );
```

to reflect the id of inserted domain record in step2. Assuming id is 2 for newly inserted record, the line would change to -

```php
define( 'CIVICRM_DOMAIN_ID' , 2 );
```

## Register a domain group

In multi-org installation, you can configure a group for each multi-site aware domain (not necessarily including the top level domain). When you login to the site and go to "manage groups" screen, you will notice a group with the name as that of domain. System requires you to register this master group responsible for holding sub-groups/contacts. Contacts using the site will be automatically added to the site. You need to set a different group for each site at `civicrm/admin/setting/preferences/multisite?reset=1`

Note that it is valid not to enable multisite on the top level domain (which is unaware of the subdomains). This improves performance by not creating such a deep hierarchy of groups

## Associate an organization with the master group

To represent a multi-org hierarchy, an organization could be connected to the master group (one-to-one relationship). Add a record in the civicrm_group_organization table to represent this (or use the MultisiteDomain.create api to create your domain). This record only has value if the multisite extension is enabled.

## Configure Access Control

Here we restrict users on Site N to contacts in Site N's Site Group.

There is a separate extension called **multisite** that automatically implements CiviCRM access control for multi-site installations, such that normal users on Site N are restricted to contacts in Site N's Site Group. Note that users with permission to view or edit all contacts bypass this access control.
