# WordPress plugins and themes incompatible with CiviCRM

Because of the huge number and ever-changing nature of community contributed Wordpress plugins, CiviCRM cannot guarantee compatibility with contributed plugins. The following information may be of use in trying to identify conflicts, Please add to/edit this information as applicable.

| Information last updated on | Plugin/Theme | Version | Problem | Workaround | Other comments | Reporter |
| --- | --- | --- | --- | --- | --- | --- |
| 2016-02-25 | Super Cache | 1.4 | Cached contribution pages don't work - presumably related to sessions / keys in Civi. _(A previous issue, now fixed: page won't cache if they have anything in $_GET. Civi adds a parameter to this, so no page is ever cached)_ | Add the CiviCRM querystring to the excluded pages in Super Cache's settings. This won't work if you use the shortcode embed method, though - you'll need to exclude each individual page (or shove the whole lot into an /civi/ basepage or something, and exclude that).

_(For the never-caching-anything problem, edit wp-cache-phase2.php to change two if statements - see details below.)_ | It seems like it'd be fairly quick to write a plugin for Super Cache that turns off the cache if it detects the shortcode: [http://ocaoimh.ie/wp-super-cache-developers/](http://ocaoimh.ie/wp-super-cache-developers/). | aglozier/krypto |
| 2014-03-31 | Ultimate TinyMCE | ? | Civi works but the editor didn't - you can't edit posts or pages | | | aglozier |
| 2014-03-31 | No Category Parents | ? | Breaks front-end pages | | | jcmorrow |
| 2014-03-31 | Yoast SEO | < 1.5.2.5 | (Reported fixed in latest version 1.5.2.5) Breaks front-end pages when Open Graph meta data added to pages. | Disable "Add Open Graph meta data" under the Social Media Facebook tab. | This appears to be fixed in 1.5.2.7 | krypto/aglozier |
| 2014-03-31 | UberMenu Sticky | | Breaks all front-end forms. | | | divemasterza |
| ? | Facebook Like Thumbnail | | | | | |
| | JetPack | all | Causes conflict with Backbone JQuery calls | Instead of deactivating Jetpack every time you need to access a profile for a contribution page or edit a date, simply disable the Notifications Module at [http://yoursite.com/wp-admin/admin.php?page=jetpack_modules](http://yoursite.com/wp-admin/admin.php?page=jetpack_modules) | | Workaround - themak |
| | "google doc embed plugin" | | [http://forum.civicrm.org/index.php/topic,36213.msg154020.html#msg154020](http://forum.civicrm.org/index.php/topic,36213.msg154020.html#msg154020) | | | |
| 2015-06-01 | Calls to Action | 2.3.x
 cv: 4.5 | [http://civicrm.stackexchange.com/questions/2920/wp-call-to-action-plugin-causes-js-errors](http://civicrm.stackexchange.com/questions/2920/wp-call-to-action-plugin-causes-js-errors)[Causes conflict with select2 lib](http://civicrm.stackexchange.com/questions/2920/wp-call-to-action-plugin-causes-js-errors) | replace with patched select2 lib from 4.6 branch
[https://raw.githubusercontent.com/colemanw/select2/09a6e7bb1f435c94869ad9e008c70f37fe60297d/select2.min.js](https://raw.githubusercontent.com/colemanw/select2/09a6e7bb1f435c94869ad9e008c70f37fe60297d/select2.min.js) | fixed in CiviCRM 4.6 |
#### ginkgomzd
 |
| 2016-02-01 | Salient Theme | 7.0.5; cv:4.6, 4.7 | Causes conflict with Backbone JQuery calls, mainly noticed in Events -> Manage -> Online Registration | | | gah242s |
| 2016-02-15 | LDD Directory Lite | 0.8.53 | Causes conflict with Backbone JQuery calls, mainly noticed in Events -> Manage -> Online Registration | | | agileware |
| 2016-02-25 | Font Emoticons | 1.4.1 | Makes text replacements in inline Javascript, e.g. with no spaces "( i )" becomes ![(info)](https://wiki.civicrm.org/confluence/s/en_GB/3398/84f448c1067609161db7eeaf020f96b084eef29d.24/_/images/icons/emoticons/information.png) causing errors in Civi's javascript. | You can conditionally use remove_filter() to stop the plugin from parsing 'the_content' when Civi content is present. Or disable the plugin. | | haystack |
| 2016-1-14 | Karma theme | 4.8.3 (and earlier) | Causes conflict with Backbone JQuery calls, mainly noticed in Events -> Manage -> Online Registration | Currently using Theme Test Drive plugin - [https://wordpress.org/plugins/theme-test-drive/](https://wordpress.org/plugins/theme-test-drive/) - as a way of enabling admin users to run a different theme when they are logged in, and so workaround the issue. Perhaps a useful thread - [http://civicrm.stackexchange.com/questions/11946/dequeue-backbone-from-theme-load-and-load-civicrm-backbone](http://civicrm.stackexchange.com/questions/11946/dequeue-backbone-from-theme-load-and-load-civicrm-backbone) | | Graham |

_SuperCache code changes (no longer needed, but keeping just in case):_

_if ( $wp_cache_no_cache_for_get && false == empty( $_GET ) && false == defined( 'DOING_CRON' ) ) {_
 _// Edit to fix CiviCRM compatability - if there's a get request that's just Civi, it's fine to cache_
 _if (!(count($_GET) == 1 && $_GET['civicrm_install_type'] == 'wordpress')) {_
 _if ( isset( $wp_super_cache_debug ) && $wp_super_cache_debug ) wp_cache_debug( "Non empty GET request. Caching disabled on settings page. " . print_r( $_GET, 1 ), 1 );_
 _return false;_
 _}_

_}_

_// Fix for CiviCRM_
_if ( (!empty( $_GET )) && (!(count($_GET) == 1 && $_GET['civicrm_install_type'] == 'wordpress')) ) {_
 _if ( isset( $GLOBALS['wp_super_cache_debug'] ) && $GLOBALS['wp_super_cache_debug'] ) wp_cache_debug( 'Supercache caching disabled. Only using wp-cache. Non empty GET request. ' . print_r( $_GET, 1 ), 5 );_
 _$super_cache_enabled = false;_
 _}_