# Version-specific upgrade tasks

In general the steps to upgrade CiviCRM are identical no matter which version you're upgrading _from_ and _to_ &mdash; however this page offers some exceptions.

Here you will find special steps needed when your upgrade crosses certain CiviCRM versions.

For example, if you are upgrading from CiviCRM 4.1 to CiviCRM 4.3, then you should check this page for *both* "CiviCRM 4.2" and "CiviCRM 4.3" since your upgrade "crosses" both of those versions.

## CiviCRM 5.29

### WordPress and "wp-content/plugins/files/civicrm"

The CiviCRM "files" folder (a.k.a.  `[civicrm.files]`) stores runtime data, such as uploaded images, log files, and
certain caches.  The folder is created automatically during installation.  However, the default location of this folder
has evolved, and some systems require a manual update.

!!! note "How has the folder changed?"

    The location of `[civicrm.files]` on WordPress has evolved in the *de facto* typical location:

    | Version | Typical location |
    | -- | -- |
    | CiviCRM 4.x | `{webroot}/wp-content/plugins/files/civicrm` |
    | CiviCRM 5.x | `{webroot}/wp-content/uploads/civicrm` |

    Additionally, there is an evolution in *how* the location is typically calculated:

    | Version | How the location is typically calculated |
    | -- | -- |
    | CiviCRM 4.x | Start from `CIVICRM_TEMPLATE_COMPILEDIR` and go to the parent folder |
    | CiviCRM 5.x (before 5.29) | Start from `CIVICRM_TEMPLATE_COMPILEDIR` and go to the parent folder|
    | CiviCRM 5.x (after 5.29) | Start from `wp_get_upload_dir()` and go to child folder, `civicrm/` |

!!! note "Why has the location evolved?"

    The original location did not match the WordPress convention for runtime data-storage.  This can create
    compatibility and installation problems for some hosting environments that depend on the WordPress conventions. 
    Using a more standardized location (and more standardized calculation) ultimately simplifies administration and
    improves compatibility.

!!! note "How do I know what my current location is?"

    There are a few techniques:

    * Look in your web filesystem for the folder (ie `wp-content/plugins/files/civicrm` or `wp-content/uploads/civicrm`).
      One or the other should exist.
    * Login to CiviCRM and navigate to `Administer => System Settings => Directories`. Click on the help ("?") icon at the
      top; a dialog will define `[civirm.files]`.  Similarly, navigate to `Administer => System Settings => Resource
      URLs`.  Click on the help ("?") icon; a dialog will define `[civicrm.files]`.

If a site currently uses `wp-content/plugins/files/civicrm`, then you should prepare in advance for 5.29 -- either by
(1) configuring the folder explicitly or (2) migrating the folder.  Below, we describe each approach and some trade-offs.

!!! tip "Approach 1: Configure the folder explicitly"

    By default, CiviCRM calculates the location of `[civicrm.files]` automatically.  However, you
    can [explicitly set the location of `[civicrm.files]`](../customize/paths.md) to match your
    actual, current location.  Any future changes in the defaults will not affect your configuration.

    Steps:

    1. Determine the local path of the current folder (ex: `/var/www/wp-content/plugins/files/civicrm`)
    2. Determine the public URL of the current folder (ex: `https://example.com/wp-content/plugins/files/civicrm`)
    3. Find and edit the file `civicrm.settings.php`. Add these options:

       ```php
       $civicrm_paths['civicrm.files']['path'] = '/var/www/wp-content/plugins/files/civicrm';
       $civicrm_paths['civicrm.files']['url'] = 'https://example.com/wp-content/plugins/files/civicrm';
       ```

    The advantage of this approach is that the change is relatively safe, clear-cut, and easy to undo.

    The disadvantage: if you ever migrate the site to a different hosting environment (or if you use a staging<=>production
    process), you will need to update `civicrm.settings.php` to use the new paths and new URLs.

!!! tip "Approach 2: Migrate the folder"

    You may reorganize the files to match the newer defaults. Initial steps:

    1. Rename the folder `wp-content/plugins/files/civicrm` to `wp-content/uploads/civicrm`.
    2. In CiviCRM, navigate to `Administer => System Settings => Directories`. If there are any explicit
       references to the old folder, change them.
    3. In CiviCRM, navigate to `Administer => System Settings => Resource URLs`. If there are any explicit
       references to the old folder, change them.
    4. Find and edit the `civicrm.settings.php`. Search for any references to `plugins/files/civicrm`
       and change them to `uploads/civicrm`.

    At this point, the folder has been formally moved - but we may not be done yet. There may still be
    external references to the old URLs or the old paths, such as:

    * Email templates which use images that were previously uploaded
    * Contact records with photo attachments
    * External links to assets in downloaded extensions
    * Backup tools and logging tools which collect information from the old folder

    How to address old references?  If your hosting environment supports "symlinks", you might create a symlink from
    the old folder to the new folder.  Alternatively, you might configure the HTTP server to do redirects, or you might
    do a global search/replace on MySQL content. Alas, the prognosis and details of these measures will depend
    on the specific deployment (*e.g.  Does it run on Linux or Windows?  Apache or nginx?  etc*) and on how the
    system was used (*e.g. Have users uploaded any images? Where are the old references?*)

    The advantage of this approach is that the configuration will be more "portable" - e.g.  you can more easily
    migrate, reproduce, or reconfigure the site for different environments.

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

