# Version-specific upgrade tasks

In general the steps to upgrade CiviCRM are identical no matter which version you're upgrading _from_ and _to_ &mdash; however this page offers some exceptions.

Here you will find special steps needed when your upgrade crosses certain CiviCRM versions.

For example, if you are upgrading from CiviCRM 4.1 to CiviCRM 4.3, then you should check this page for *both* "CiviCRM 4.2" and "CiviCRM 4.3" since your upgrade "crosses" both of those versions.

## CiviCRM 5.29

### WordPress and `[civicrm.files]`

The CiviCRM "files" folder (a.k.a.  `[civicrm.files]`) stores runtime data, such as uploaded images, log files, and
certain caches.  The folder is created automatically during installation.  However, the default location of this folder
has evolved, and some systems require a manual update.

!!! note "How has the folder changed?"

    During CiviCRM 4.x, the *de facto*, typical location changed:

    | Version | Typical location |
    | -- | -- |
    | CiviCRM <=4.6 | `{WEB_ROOT}/wp-content/plugins/files/civicrm/` |
    | CiviCRM >=4.7 | `{WEB_ROOT}/wp-content/uploads/civicrm/` |

    Additionally, in Civi 5.29, the technical rule for calculating the location changed subtly:

    | Version | Technical rule to determine default location |
    | -- | -- |
    | CiviCRM <=5.28 | Start from `CIVICRM_TEMPLATE_COMPILEDIR` and go to the parent folder |
    | CiviCRM >=5.29 | Start from `wp_get_upload_dir()` and go to child folder, `civicrm/` |

!!! note "Which systems are OK? Which systems have a problem?"

    On many CiviCRM-WordPress deployments, the new rule and the old rule agree - they both output
    `wp-content/uploads/civicrm/`.  These deployments are OK.

    However, on some deployments, the new rule and old rule may not agree. This is likely to happen if the site
    originated on v4.6 or if the path has `wp-content/plugins/files/civicrm/`. These sites require some extra maintenance.

!!! note "Why has the location evolved?"

    The original location did not match the WordPress convention for runtime data-storage.  For some environments and
    configurations, this created compatibility or installation problems.  With the changes in 4.7 and 5.29, CiviCRM is
    better aligned with the convention - leading to better compatibility.

!!! note "How do I know what the true location is?"

    Look in the filesystem to determine where there is actual data. In particular, look at both:

    * `wp-content/plugins/files/civicrm`
    * `wp-content/uploads/civicrm`

    If only one folder exists, then that's it.

!!! note "What if both folders exist?"

    This has been observed in some scenarios - e.g.  if you attempted an upgrade to certain versions of CiviCRM without
    laying groundwork from this guide, then CiviCRM may have auto-created a second set of placeholder folders.

    The best thing is to dig into the filesystem and confirm whether *real content* exists in the subfolders.

    * Important content can live in the subfolders `custom/`, `ext/`, `persist/contribute/`, and `upload/`.
    * You can disregard any empty folders or any placeholder files (`.htaccess`, `index.html`).
    * You can disregard `templates_c` or `dyn`. These are auto-generated and disposable.
    * On the command-line, the commands `find`, `find -type f`, and `du` can help to locate content.

    If the only genuine content is in `wp-content/plugins/files/civicrm`, then proceed with that.

    If you cannot determine the one true location, then you may wish to discuss the details on Mattermost (`WordPress`) or StackExchange.

Manual intervention is appropriate if the true content lives in `{WEB_ROOT}/wp-content/plugins/files/civicrm`. These
steps will ensure that the content continues to be available in 5.29.

The most clear-cut resolution is to configure the folder explicitly.  Alternatively, you may migrate or merge the
folder into the newer location.  Below, we describe each approach and some trade-offs.

!!! tip "Approach 1: Configure the folder explicitly"

    The aim of this approach is to preserve the original location of `[civicrm.files]`.  If there are any external
    references to the original location (such as hyperlinks or backup tools), they will continue to work.  We can
    achieve this by [explicitly setting the location in `civicrm.settings.php`](../customize/paths.md).  It will not
    matter if the default values change.

    Steps:

    1. Determine the correct, local path of the current folder (ex: `/var/www/wp-content/plugins/files/civicrm`)
    2. Determine the correct, public URL of the current folder (ex: `https://example.com/wp-content/plugins/files/civicrm`)
    3. Find and edit the file `civicrm.settings.php`. Add the correct path and URL:

       ```php
       $civicrm_paths['civicrm.files']['path'] = '/var/www/wp-content/plugins/files/civicrm';
       $civicrm_paths['civicrm.files']['url'] = 'https://example.com/wp-content/plugins/files/civicrm';
       ```

    The advantage of this approach is that the change is relatively narrow, clear-cut, and easy to undo.

    The disadvantage: if you ever migrate the site to a different hosting environment (or if you use a staging<=>production
    process), you will need to update `civicrm.settings.php` again.

!!! tip "Approach 2: Migrate the folder"

    The aim of this approach is to re-arrange your filesystem to match the new defaults.  This makes the configuration
    "thinner" or "more portable", but it may require more expertise and attention.

    The initial steps are:

    1. Rename the folder `wp-content/plugins/files/civicrm` to `wp-content/uploads/civicrm`.
    2. In CiviCRM, navigate to `Administer => System Settings => Directories`. If there are any explicit
       references to the old folder, change them.
    3. In CiviCRM, navigate to `Administer => System Settings => Resource URLs`. If there are any explicit
       references to the old folder, change them.
    4. Find and edit the file `civicrm.settings.php`. Search for any references to `plugins/files/civicrm`
       and change them to `uploads/civicrm`.

    At this point, the folder has been formally moved - but we may not be done yet. There may still be
    external references to the old URLs or the old paths, such as:

    * Email templates which use images that were previously uploaded
    * Contact records with photo attachments
    * External links to assets in downloaded extensions
    * Backup tools and logging tools which collect information from the old folder

    How to address old references?  If your hosting environment supports "symlinks", you might create a symlink from
    the old folder to the new folder.  Alternatively, you might configure the HTTP server to do redirects, or you might
    do a global search/replace on URLs that appear in MySQL content. Alas, the prognosis and details of these measures will depend
    on the specific deployment (*e.g.  Does it run on Linux or Windows?  Apache or nginx?  etc*) and on how the
    system was used (*e.g. Have users uploaded any images? Where are the old references?*)

## CiviCRM 5.0

### Flush CiviCRM cache

After upgrading from CiviCRM 4.7.31, the cache must be flushed to resolve a mailing bug in the generated javascript. The upgrade web interface will take care of this automatically, but if you are running the database migration on the command-line, run `cv flush` to cleanup caches.

## CiviCRM 4.3

### Settings changes

**Background:** Settings are usually stored in the civicrm_setting table, where they're easily maintained. But there are occasions when developers might find it useful to override them. For instance, the development environment might mirror the production environment, except for a handful of settings. Adding a few lines to the civicrm.settings.php file can be much easier and less error-prone than changing production settings for development.

The settings in the `civicrm_setting` table are keyed on `domain`, `group`, and `name`. Here's an example using the CiviCRM 4.1 mechanism:

```
define('URL Preferences.customCSSURL', 'http://example.com/css');...
```

The `domain` is determined by the civicrm.settings.php file, the _group_ is 'URL Preferences', the _name_ is 'customCSSURL', and the `value` is `http://example.com/css`. If you're uncertain of the valid values these can take, inspect the `civicrm_setting` table.

**The change:** In CiviCRM 4.3 and later, the format has changed to:

```
global $civicrm_setting;
```

```
$civicrm_setting['URL Preferences']['customCSSURL'] = 'http://example.com/css';...
```

### Password must be supplied to cron

If you are running scheduled jobs using `CLI.php`, you will need to reconfigure cron to include a password. Scheduled jobs will no longer run if the password is not provided..


## CiviCRM 4.2

All CiviCRM-related cron jobs will stop working as soon as any site is upgraded from 4.1.x and below. If you are upgrading from a version prior to 4.2, cron jobs will need to be reconfigured using the new [Scheduled Jobs](../setup/jobs.md) method.

