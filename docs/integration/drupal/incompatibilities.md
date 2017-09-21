# Drupal modules incompatible with CiviCRM

If you discover a compatibility issue then add it to the table below. Also, log an issue in the Drupal module's issue queue and add a link to it here. Other developers can then help fix these issues more easily.

| Information last updated on | Module | Version | Problem | Link to issue | Workaround | Other comments | Reporter |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2015-05-27 | Devel | 7.x-1.5 | The 'Display page timer' setting breaks CiviCRM's JavaScript interfaces | TBC | Disable this setting | | John.K |
| 2015-06-09 | Boxes | [7.x-1.2](https://www.drupal.org/node/2295431) | CKEditor does not always work in CiviCRM when boxes is enabled | [https://www.drupal.org/node/2175471](https://www.drupal.org/node/2175471) | Apply patch [https://www.drupal.org/files/issues/boxes-civicrm_wysiwyg-2175471-1.patch](https://www.drupal.org/files/issues/boxes-civicrm_wysiwyg-2175471-1.patch) | | ckng |
| 2015-09-10 | google_tag | 7.x-1 | Create new Mailer will break page completely | [https://www.drupal.org/node/2566493](https://www.drupal.org/node/2566493) | explicitly remove page and / or role | | Shawn H. |