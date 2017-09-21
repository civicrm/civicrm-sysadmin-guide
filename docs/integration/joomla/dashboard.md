# Configuring Front-end Contact Dashboard in Joomla! Sites

### Configuring a Front-end Contact Dashboard in Joomla! Sites

CiviCRM includes the ability to expose a **Contact Dashboard** which gives logged in front-end users the ability to view their subscribed groups, membership(s), contribution(s) and registered events. This section covers adding a link to this page to your front-end site menus.

#### Verify that the Contact Dashboard works properly in the your Joomla! Administrator interface.

* Navigate to **My Contact Dashboard** - linked from the **CiviCRM Shortcuts** block on any CiviCRM page.
    * You should see contact-related information for the contact that is linked to "your" (i.e. administrator) record.

#### Add "My Contact Dashboard" as a menu item using the Administrator Menu Manager

* Login to Administrator if not already logged in.
* Click **Menu** on top navigation bar and select the menu you want to add the link to (for example - **mainmenu** ).
* Click **New** icon (top right).
* Click **Menu Item Type** and select **CiviCRM Dashboard** from the civicrm section
* Enter a **Name** for the menu item (e.g. "Contact Dashboard").
* Complete the remaining fields (Parent Item, Access Level, Published).

!!! note **Do NOT use PUBLIC Access Level for this menu item. Users must be logged in to the front-end for this feature to work. Otherwise CiviCRM has not way to identify the person's contact record.** |

 .
* Click **Save & Close.**

#### Verify permissions are set correctly in CiviCRM

* Navigate to your CiviCRM Home.
* From the **Administer** menu, choose **Users & Permissions** , then **Permissions (Access Control)**
* Select **Joomla Access Control**
* Under **Registered, l** ook for **CiviCRM: Access Contact Dashboard.** Make sure this is set to **Allowed.**
* Scroll to the top and click **Save & Close.**

#### Navigate to your front-end site and test out the your new menu item.