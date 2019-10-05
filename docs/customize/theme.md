# Theme information

CiviCRM contains within the system some base style content to ensure that the user interface has a standard look and feel. However your chosen CMS theme can also affect this look and theme as well. CiviCRM also now supports the ability to install a theme for CiviCRM which can load in bootstrap related CSS or override or hide the standard civicrm.css file that is loaded by default. This gives System administrtors much more control over the look and feel of CiviCRM for their users.

!!! note CMS theme and CiviCRM Theme
   CMSes still have their own theme subsystem which may clash or interact with the chosen CiviCRM theme. When creating a CMS theme or CiviCRM theme you need to be concious of the potential interactions between the chosen themes. You may also choose to select to not have a CiviCRM theme all together and then just have all the styling through the CMS theme.

## CMS Integration

The CiviCRM theme subsystem allows you to make decisions about what CiviCRM theme should be used. The options avaliable will depend on the CMS that you choose to use. For Drupal and Backdrop you can only choose a theme that will apply for front end CiviCRM pages such as a Contribution Page and also for back office / administration pages such as the advanced search, Contact summary screen etc. For WordPress, Joomla you will be able to make decisions on what theme to use if its a frontend page or if it is a backend page. 

### What determines frontend pages from backend pages

For Drupal and Backdrop the test is prescenece of `is_public` key in the menu route xml for the perticular URL. For WordPress and Joomla the test is whether or not `is_frontend` configuration paramenter is true or not.

## Installing a CiviCRM theme.

To install a CiviCRM theme, you will need to download an extension that implements `hook_civicrm_theme` an example extension that does this is the [Shoreditch extension](https://civicrm.org/extensions/shoreditch). Once you have downloaded and enabled the extension you should then navigate to the display prefences at Administer -> Customize Data and Screens -> Display Prefecnes and then set the theme you wish to use in the theme configuration.


