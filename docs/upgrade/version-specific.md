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

    The *de facto* typical location changed:

    | Installer version | Typical location |
    | -- | -- |
    | CiviCRM <=4.6 | `{WEB_ROOT}/wp-content/plugins/files/civicrm/` |
    | CiviCRM >=4.7 | `{WEB_ROOT}/wp-content/uploads/civicrm/` |

    Additionally, the technical rule for calculating the location changed subtly:

    | Version | Technical rule to determine default location |
    | -- | -- |
    | CiviCRM <=5.28 | Start from `CIVICRM_TEMPLATE_COMPILEDIR` and go to the parent folder |
    | CiviCRM >=5.29 | Start from `wp_get_upload_dir()` and go to child folder, `civicrm/` |

    For many deployments, the change in the technical rule will still give the same outcome. However,
    if a site originated on <=4.6, then the `wp_get_upload_dir()` rule may cause breakage.

    __Special aside__: Early revisions of 5.27 (5.27.0-5.27.3) included an update to use `wp_get_upload_dir()`;
    however, this did not provide adequate documentation/support for the migration.  It was rolled back in 5.27.4 and
    deferred to 5.29.

!!! note "Why has the location evolved?"

    The original location did not match the WordPress convention for runtime data-storage.  For some environments and
    configurations, this created compatibility or installation problems.  With the changes in 4.7 and 5.29, CiviCRM is
    better aligned with the convention - leading to better long-term compatibility.

!!! note "How do I know what the real location is?"

    Use a shell or file-manager to examine the filesystem and determine where there is actual data.  In particular,
    look at both:

    * `wp-content/plugins/files/civicrm`
    * `wp-content/uploads/civicrm`

    If only one folder exists, then that's it.

    It is possible that both folders exist.  This may happen if (a) an older site was upgraded to 5.27.0/5.29.0
    without preparation, or (b) a site previously did a partial migration.

    The existence of two folders may indicate a split in the real data - or there may simply be extraneous placeholders.  You need to
    examine to the subfolders to find any important content. Note:

    * Important content can often live in the subfolders `custom/`, `ext/`, `persist/contribute/`, and `upload/`.
    * Empty folders or any placeholder files (`.htaccess`, `index.html`) are disposable.
    * `templates_c` or `dyn` are auto-generated and disposable.

    If using a command-line, the commands `find`, `find -type f`, and `du` can help to skim or measure content.

After determining where the data currently lives, we can suggest a course of action:

| Current data location | Recommended action |
| -- | -- |
| Only `wp-content/uploads/civicrm` has real data | No change needed |
| Only `wp-content/plugins/files/civicrm` has real data | See "How To: Configure the folder explicitly" |
| Both folders have real data | See "How To: Merge or migrate the folder" |
| Neither folder has real data | It doesn't matter what you do |

!!! tip "How To: Configure the folder explicitly"

    The aim of this approach is to preserve the original location of `[civicrm.files]`.  If there are any external
    references to the original location (such as hyperlinks or backup tools), they will continue to work.  We can
    achieve this by [explicitly setting the location in `civicrm.settings.php`](../customize/paths.md).

    Steps:

    1. Determine the correct, local path of the current folder (ex: `/var/www/wp-content/plugins/files/civicrm`)
    2. Determine the correct, public URL of the current folder (ex: `https://example.com/wp-content/plugins/files/civicrm`)
    3. Find and edit the file `civicrm.settings.php`. Add the correct path and URL:

       ```php
       $civicrm_paths['civicrm.files']['path'] = '/var/www/wp-content/plugins/files/civicrm';
       $civicrm_paths['civicrm.files']['url'] = 'https://example.com/wp-content/plugins/files/civicrm';
       ```

!!! tip "How To: Merge or migrate the folder"

    The aim of this approach is to re-arrange your filesystem to match the new defaults.  This makes the configuration "thinner" or "more
    portable", and it can resolve a split, but it may require more expertise and attention.  Be sure to read the full instructions first.

    First, make sure you have a current backup of the files and the database.  (If there's a problem, you may want to rollback.)

    Second, migrate the folder `wp-content/plugins/files/civicrm` to `wp-content/uploads/civicrm`.  This may be as simple as renaming the
    folder.  However, if the target folder already exists, then you'll need to move files on a per-sub-folder basis.  Alternatively, in a
    Unix shell, you can use the `rsync` command, e.g.

    ```bash
    ## Dry run - Preview the files to sync/merge
    rsync --dry-run -uva wp-content/plugins/files/civicrm/./ wp-content/uploads/civicrm/./

    ## Actual run
    rsync -uva wp-content/plugins/files/civicrm/./ wp-content/uploads/civicrm/./

    ## Remove the old folder
    mv wp-content/plugins/files/civicrm /tmp/removed-files
    ```

    Third, with the files in place, you should update any CiviCRM settings which reference these paths:

    1. Find and edit the file `civicrm.settings.php`. Search for any references to `plugins/files/civicrm`
       and change them to `uploads/civicrm`.
    2. In CiviCRM, navigate to `Administer => System Settings => Directories`. If there are any explicit
       references to the old folder, change them.
    3. In CiviCRM, navigate to `Administer => System Settings => Resource URLs`. If there are any explicit
       references to the old folder, change them.

    Finally, we come to the most open-ended step.  There may still be external references to the old URLs or the old paths, and they should
    be transitioned.  For example, references may be found in:

    * Email templates which use images that were previously uploaded
    * Contact records with photo attachments
    * External links to assets in downloaded extensions
    * Backup tools and logging tools which collect information from the old folder

    How to address external references?  Alas, the prognosis and details will depend on the specific deployment.
    However, here a few ideas to consider:

    * Create a sym-link or hard-link from the old folder to the new folder (if your hosting environment supports sym-links or hard-links).
    * Configure the HTTP server to redirect old URLs to new URLs.
    * Perform aggressive search/replace operations (in the filesystem and the database).

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

