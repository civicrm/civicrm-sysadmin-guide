# WordPress plugins and themes incompatible with CiviCRM

Because of the huge number and ever-changing nature of community contributed WordPress plugins, CiviCRM cannot guarantee compatibility with contributed plugins. The following information may be of use in trying to identify conflicts, Please add to/edit this information as applicable.


## JetPack

* Version: all
* Information last updated on:  
* Reporter: Workaround - themak
* Problem: Causes conflict with Backbone JQuery calls
* Workaround:

    Instead of deactivating Jetpack every time you need to access a profile for a contribution page or edit a date, simply disable the Notifications Module at `http://example.org/wp-admin/admin.php?page=jetpack_modules`
    
* Other comments:  

## Slider Revolution

* Information last updated on: 2018-05-17
* Reporter: samuelsov
* Problem:

    CSS z-index problem - see also https://civicrm.stackexchange.com/questions/16560/civicrm-select-box-items-cant-be-selected-in-wordpress-page-create/18673?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa
    
* Workaround:

    See https://civicrm.stackexchange.com/a/24991/72
    
## Elementor

  * Information last updated on: 2018-12-05
  * Reporter: samuelsov
  * Problem:
  
    Some smarty variable don't get replaced. For example, on a contribution page, the profile list are all shown as <%= title =%>
    
## Qode Interactive themes/Bridge theme

* Version: 16.6
* Information last updated on: 2019-03-11
* Reporter: Noah Miller/Lemniscus
* Problem:

    Among other problems, the theme's CSS and Javascript heavily target the `.content` class, which CiviCRM uses extensively but in different places, resulting in public-facing CiviCRM forms where elements are displaced or hidden.
