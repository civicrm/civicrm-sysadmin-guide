# Directories

##### Return to [System Settings](https://wiki.civicrm.org/confluence/display/CRMDOC/System+Settings) Main Page

### Directories - Configure directories in your file system for temporary files, images, custom files, custom templates, case configuration files, and custom php files.

Click Directories which brings you to the Upload Directories page.
 Default values are supplied for these upload directories the first time you access CiviCRM
 based on the CIVICRM_TEMPLATE_COMPILEDIR file specified in civicrm.settings.php. If you need to modify the defaults, make sure that your web server has write access to the directories.

Indicate a path on your server or network for the item(s) you wish to setup - temporary files, images, custom files and/or custom templates.

For example, a temporary file path might look like this: /home/drupal/public_html/drupal/files/civicrm/upload/

Click Save to save your action or Cancel to cancel it.

If successful you will see the message "Your settings changes have been saved."

!!! danger "Images Path"

    The **Images** directory should be specified without the use of symbolic links. Using symbolic links in this field has been observed to cause an "Unknown error" message when attempting to upload images or refresh image thumbnails in the email editor. This is due to a change in the kcfinder package version 3.12 introduced in CiviCRM version 4.5.3.


!!! danger "Custom Files Path"

    The current "default" setting for the **Custom Files** directory is below the default path for **Temporary Files**. This is potentially dangerous, as running the _directoryCleanup_ debug action could delete all custom files. If you are using custom files, you should modify the path so it is NOT below your Temporary Files directory.


### Custom Templates

To create custom templates, simply create a folder (ex. drupal/sites/all/civicrm_templates ). And add the full system path into the custom templates section on the directories page.

To modify a template (ex. civicrm/templates/CRM/History/Selector/Activity.tpl ) simply copy the path and file in your custom templates directory (ex. drupal/sites/all/civicrm_templates/CRM/History/Selector/Activity.tpl ) You can then modify the custom template. CiviCRM searches your custom template directory and if it finds a template, it will use that instead of the stock template.

Now when you upgrade CiviCRM your custom templates will not be overwritten.

!!! tip "Add Custom JavaScript Functions to Templates"

    As of version 3.4.3 / 4.0.3 CiviCRM will also check your Custom Templates Directories for files to append to a given template. These "extra" files should be named using the root template file name + 'extra.tpl'. For example, if you want to append some jQuery logic (or crmAPI calls, or ....) to the CiviEvent Dashboard - add them to this file in your custom templates directory tree: _CRM/Event/Page/Dashboard.extra.tpl_.

    Note that you will need to start your "extra" custom file with the Smarty {literal} tag and then use {/literal} if you need to use any Smarty code or variables.


!!! note "Case configuration files"

    If you are using the CiviCase component, your [Case configuration files](https://wiki.civicrm.org/confluence/display/CRMDOC/CiviCase+Configuration) should also be stored under your custom templates path so they are not affected by upgrades. These should be placed in _<custom templates path>/CRM/Case/xml/configuration_.


### Custom php files

These are similar to custom templates in theory to php files that sit in this directory will over-ride the php files in the main CiviCRM fileset. They will not be overwritten when an upgrade takes place (which keeps your changes but can cause problems if the file has been upgraded and you don't port the changes across to your customised file)