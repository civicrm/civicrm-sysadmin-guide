# WordPress plugins and themes incompatible with CiviCRM

Because of the huge number and ever-changing nature of community contributed WordPress plugins, CiviCRM cannot guarantee compatibility with contributed plugins. The following information may be of use in trying to identify conflicts, Please add to/edit this information as applicable.
    
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
