# Configuring Front-end Profile Listings and Forms in Joomla Sites

CiviCRM includes the ability to expose Profile forms and listings, as well as Online Contribution Pages, to users and visitors of the 'front-end' of your Joomla! site. This section covers adding Profile pages to your front-end site menus. Refer to [this page](https://wiki.civicrm.org/confluence/display/CRMDOC/Displaying+Online+Contribution+Pages+in+Joomla%21+Frontend+Sites) for contribution page setup.

## Create profiles

Create one or more Profiles (sets of fields for input forms and/or search and listing pages).

Refer to the main [Profiles Admin](https://wiki.civicrm.org/confluence/display/CRMDOC/Profiles+Admin) page for an overview and general information on creating Profiles.

## Test profiles

Verify that the Profile(s) that you want to include in your front-end site menu(s) work properly in the your Joomla Administrator interface.

* Navigate to the following URL to test a search/listings page:
**<site root>/administrator/index2.php?option=com_civicrm&task=civicrm/profile&reset=1&gid=N**
    * replacing **N** with the **ID** of your Profile
* Navigate to the following URL to test a contact create/sign-up form:
**<site root>/administrator/index2.php?option=com_civicrm&task=civicrm/profile/create&reset=1&gid=N**
    * replacing **N** with the **ID** of your Profile
* Navigate to the following URL to test editing a logged in user's form:
**<site root>/administrator/index2.php?option=com_civicrm&task=civicrm/profile/edit&reset=1&gid=N**
* replacing **N** with the **ID** of your Profile
* Note the ID(s) of the Profile(s) you want to add to your front-end menus - you'll need them shortly.

## Create menus

Create the front-end menus using Menu Manager

* Login to Administrator if not already logged in.
* Click **Menus** on top navigation bar and select the menu you want to add the Profile link(s) to (for example - Main Menu).
* Click **New** icon (top right).
* Click **CiviCRM** link from the **Internal Link** list.
* Click one of the options under **CiviCRM Profile** (depending on whether you are creating a link for Create, Edit, Search or View profiles).
* Enter a **Title** for the menu item under Menu Item Details.
* Modify other details in the left pane (Parent Item, Access Level, Published) based on your requirements for this menu item.
    * **Profile-View and Profile-Edit require "Registered" as the Access Level.** Users need to have an account and be logged in to the front-end in order for CiviCRM to retrieve their own contact information for viewing or editing in a Profile.
* In the **Parameters-Basic** section (upper right):
    * Select the title of the CiviCRM Profile you want to expose with this front-end menu item from the "Select a Profile" drop-down.
    * If you don't see any CiviCRM Profiles listed in the dropdown - review the Trouble-shooting tip below for sites with CiviCRM and Joomla in separate databases.
* Repeat these steps as needed to add additional Profile menu items.

## Test menu items

Navigate to your front-end site and test out the your new menu items.

## Trouble-shooting

### Front-end menus broken when CiviCRM is in a separate DB from Joomla!

The solution is to change all template xml files in views directory for e.g -

JOOMLA/components/com_civicrm/views/Contributions/tmpl/default.xml
 JOOMLA/components/com_civicrm/views/Contributions/tmpl/pcpinfo.xml
 ........
 JOOMLA/components/com_civicrm/views/*/tmpl/*.xml

* to change query to include db prefix against any civicrm table used.

For e.g for the file - JOOMLA/components/com_civicrm/views/Contributions/tmpl/pcpinfo.xml
 Change following line -

```
<param name="id" type="sql" default="10" label="Personal Campaign Page" query="SELECT id, title FROM civicrm_pcp WHERE is_active = 1 AND status_id = 2 ORDER BY title" key_field="id" value_field="title" />
```

to -

```
<param name="id" type="sql" default="10" label="Personal Campaign Page" query="SELECT id, title FROM CIVICRM_DB.civicrm_pcp WHERE is_active = 1 AND status_id = 2 ORDER BY title" key_field="id" value_field="title" />
```

where CIVICRM_DB is the civicrm database.

* give joomla database user a select permission on civicrm database.

### Fixing Problems with Module Visibility

The visibility for Joomla modules are based on menu links (ItemID). Some CiviCRM forms exposed through the frontend will lose the url-based connection to their menu item as you navigate deeper into the form, and consequently any modules associated with that menu item will disappear. To work around this, create a new menu for link generating purposes, set it to the "Special" user or hide it in a non-visible module position. Then create a url menu item and enter the url of the page that experienced the disappearing modules. Return to your module settings and add this new menu link to the list of pages the module should be visible on.