# Theme information

CiviCRM contains within the system some base style content to ensure that the user interface has a standard look and feel. However your chosen CMS theme can also affect this look and theme as well. CiviCRM, since v5.16, has the ability to install a CiviCRM theme which can load custom CSS, images and/or other assets. The theming system expands the existing ability to override or hide the standard `civicrm.css` file that is loaded by default by introducing granular options to disable the stock CiviCRM CSS on the front-end or back-end. This gives System administrators much more control over the look and feel of CiviCRM for their users.

!!!note CMS theming system interactions
    The various CMS integrations still have their own theming systems which may clash or interact with the chosen CiviCRM theme. When creating a CMS theme or CiviCRM theme you need to be conscious of the potential interactions between the chosen themes. You may also choose not to have a CiviCRM theme altogether and have all style customisation handled through the CMS theme.

## CMS Integration

The CiviCRM theming subsystem allows you to make decisions about which CiviCRM theme should be used. The options available will depend on the CMS that you choose to use. For Drupal and Backdrop you can choose a theme which will apply for front-end CiviCRM (e.g: Contribution Pages) and also for back-end CiviCRM (e.g: Advanced Search, Contact Summary). For WordPress and Joomla you will be able to make decisions on what theme to use if its a front-end page or if it is a back-end page.

### How do we know if a page is Front or Back-end?

For Drupal and Backdrop the test is presence of the `is_public` key on the menu route for the requested URL. For WordPress and Joomla the test is whether or not the `is_frontend` configuration parameter is true.

## Installing a CiviCRM theme

To install a CiviCRM theme, you will need to download an extension that implements [`hook_civicrm_themes`](https://docs.civicrm.org/dev/en/latest/hooks/hook_civicrm_themes/) an example extension that does this is the [Shoreditch](https://civicrm.org/extensions/shoreditch) extension. Once you have downloaded and enabled the extension you should then navigate to the display preferences at **Administer -> Customize Data and Screens -> Display Preferences** and then set the theme you wish to use.


