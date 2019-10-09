# WordPress plugins and themes incompatible with CiviCRM

Because of the huge number and ever-changing nature of community contributed WordPress plugins, CiviCRM cannot guarantee compatibility with contributed plugins. The following information may be of use in trying to identify conflicts, Please add to/edit this information as applicable.

## Super Cache

* Version: 1.4
* Information last updated on: 2016-02-25
* Reporter: aglozier/krypto
* Problem: 

    Cached contribution pages don't work - presumably related to sessions / keys in Civi.
    (A previous issue, now fixed: page won't cache if they have anything in $_GET. Civi adds a parameter to this, so no page is ever cached)

* Workaround: 

    Add the CiviCRM querystring to the excluded pages in Super Cache's settings. This won't work if you use the shortcode embed method, though - you'll need to exclude each individual page (or shove the whole lot into an /civi/ basepage or something, and exclude that).
    
    (For the never-caching-anything problem, edit wp-cache-phase2.php to change two if statements - see details below.) 

* Other comments:

    It seems like it'd be fairly quick to write a plugin for Super Cache that turns off the cache if it detects the shortcode: http://ocaoimh.ie/wp-super-cache-developers/.

## Ultimate TinyMCE

* Version: ?
* Information last updated on: 2014-03-31
* Reporter: aglozier
* Problem: Civi works but the editor didn't - you can't edit posts or pages
* Workaround:  
* Other comments:  

## No Category Parents

* Version: ?
* Information last updated on: 2014-03-31
* Reporter: jcmorrow
* Problem: Breaks front-end pages
* Workaround:  
* Other comments:  

## Yoast SEO

* Version: < 1.5.2.5
* Information last updated on: 2014-03-31
* Reporter: krypto/aglozier
* Problem:

    (Reported fixed in latest version 1.5.2.5) Breaks front-end pages when Open Graph meta data added to pages.
    
* Workaround:

    Disable "Add Open Graph meta data" under the Social Media Facebook tab.
    
* Other comments: This appears to be fixed in 1.5.2.7

## UberMenu Sticky

* Version:  
* Information last updated on: 2014-03-31
* Reporter: divemasterza
* Problem: Breaks all front-end forms.
* Workaround:  
* Other comments:  

## Facebook Like Thumbnail

* Version:  
* Information last updated on: ?
* Reporter:  
* Problem:  
* Workaround:  
* Other comments:  

## JetPack

* Version: all
* Information last updated on:  
* Reporter: Workaround - themak
* Problem: Causes conflict with Backbone JQuery calls
* Workaround:

    Instead of deactivating Jetpack every time you need to access a profile for a contribution page or edit a date, simply disable the Notifications Module at `http://example.org/wp-admin/admin.php?page=jetpack_modules`
    
* Other comments:  

## "google doc embed plugin"

* Version:  
* Information last updated on:  
* Reporter:  
* Problem: http://forum.civicrm.org/index.php/topic,36213.msg154020.html#msg154020
* Workaround:  
* Other comments:  

## Calls to Action

* Version: 2.3.x
cv: 4.5 
* Information last updated on: 2015-06-01
* Reporter: ginkgomzd
* Problem: 

    http://civicrm.stackexchange.com/questions/2920/wp-call-to-action-plugin-causes-js-errors

    Causes conflict with select2 lib
    
* Workaround:

    replace with patched select2 lib from 4.6 branch
    https://raw.githubusercontent.com/colemanw/select2/09a6e7bb1f435c94869ad9e008c70f37fe60297d/select2.min.js 

* Other comments: fixed in CiviCRM 4.6

## Salient Theme

* Version: 7.0.5; cv:4.6, 4.7
* Information last updated on: 2016-02-01
* Reporter: gah242s
* Problem: 

    Causes conflict with Backbone JQuery calls, mainly noticed in Events -> Manage -> Online Registration
    
* Workaround:  
* Other comments:  

## LDD Directory  Lite

* Version: 0.8.53
* Information last updated on: 2016-02-15
* Reporter: agileware
* Problem: 

    Causes conflict with Backbone JQuery calls, mainly noticed in Events -> Manage -> Online Registration
    
* Workaround:  
* Other comments:  

## Font Emoticons

* Version: 1.4.1
* Information last updated on: 2016-02-25
* Reporter: haystack
* Problem: 

    Makes text replacements in inline Javascript, e.g. with no spaces "( i )" becomes causing errors in Civi's javascript.
    
* Workaround:

    You can conditionally use remove_filter() to stop the plugin from parsing 'the_content' when Civi content is present. Or disable the plugin.
    
* Other comments:  

## Karma theme

* Version: 4.8.3 (and earlier)
* Information last updated on: 2016-1-14
* Reporter: Graham
* Problem: 

    Causes conflict with Backbone JQuery calls, mainly noticed in Events -> Manage -> Online Registration
    
* Workaround:

    Currently using Theme Test Drive plugin - https://wordpress.org/plugins/theme-test-drive/ - as a way of enabling admin users to run a different theme when they are logged in, and so workaround the issue.
Perhaps a useful thread - http://civicrm.stackexchange.com/questions/11946/dequeue-backbone-from-theme-load-and-load-civicrm-backbone

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
