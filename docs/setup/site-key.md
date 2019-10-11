# Configuring your Site Key

If you are triggering jobs using the URL method, you must manually generate a site key, and then add it to your CiviCRM settings file.

1. First generate the key. We recommend using a 16-32 bit alphanumeric/punctuation key value.

    !!! warning
   
        Make sure there that there are no reserved or unsafe URL characters in your site key. These include spaces and the following characters:
       
        ```
        & = + $ ? % , / : { } # | '
        ```

    !!! tip
        You can use the following shell command to generate a key

        ```
        $ php -r 'echo md5(uniqid(rand(), true)) . " ";'
        ```

1. Open your settings file - `civicrm.settings.php` - in your favorite editor and either add or edit the `CIVICRM_SITE_KEY` value.
   
    * **in Drupal** , `civicrm.settings.php` is located in `/sites/default`.
   
    * **in Joomla,** there are 2 `civicrm.settings.php` files. By default these are located in `site/administrator/components/com_civicrm` and `site/components/com_civicrm` - it's important to add the same key to both files as some Joomla front-end functions rely on the key (eg, decrypting the Outbound Mail SMTP Password).
   
    * **in WordPress** , `civicrm.settings.php` is located in : For versions 4.6 and Prior `/wp-content/plugins/civicrm`. Starting in 4.7 for new installs `CONTENT-DIR/uploads/civicrm`

1. Depending on which version of CiviCRM you've installed, you may have this setting in your file already with a value of `NULL`, or you may need to add the line. In either case, replace `NULL` with the value of your key.
   
    !!! success "Example"
        If my generated site key value is: `3cx4aNkpQwxmM1hTMV~!B09iO6` then my settings file should look like this:
   
        ```
        define( 'CIVICRM_SITE_KEY', '3cx4aNkpQwxmM1hTMV~!B09iO6' );
        ```
