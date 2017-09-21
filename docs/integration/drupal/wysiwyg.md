# Configuring CiviCRM to Use the Default Drupal Editor

CiviCRM starting with versions 3.4.2/4.0.2 include a feature that allows administrators for sites running Drupal and the [WYSIWYG API](http://drupal.org/project/wysiwyg) module to use Drupal input formats (text formats in Drupal 7) -- including associated WYSIWYG profiles -- as the rich text editor in CiviCRM. This provides a more consistent experience for users on the Drupal platform, as the rich text editor can continue to look and act the same when editing rich text in CiviCRM as it does in native Drupal forms.

## Required or useful modules

Here's a quick list of modules that are either required or very helpful when using the Default Editor option:

* [WYSIWYG API](http://drupal.org/project/wysiwyg)
* [IMCE](http://drupal.org/project/imce)
* [IMCE WYSIWYG bridge](http://drupal.org/project/imce_wysiwyg)
* [Pathologic](http://drupal.org/project/pathologic)

## Installation and Configuration

Configuring CiviCRM to use Drupal as the default editor is quite simple.

1. Head to Administer > Customize Data & Screens > Display Preferences (screen and form configuration).
1. For WYSIWYG Editor choose "Drupal Default Editor"
1. Optionally change the default input format that will be selected when using a form that has a rich text control.

Once set, CiviCRM will use Drupal's method of rendering and processing rich text editor controls. You'll need WYSIWYG API installed for this to work (in fact the option won't appear if WYSIWYG is not installed). Input formats that are configured to use a WYSIWYG profile will have that profile function when the input format is selected. You can also enable/disable the rich text editor and change input formats just as you would in a native Drupal context.

## _Input_ formats are really _output_ formats

An important difference in the way rich text editors work in Drupal versus the way they work in CiviCRM, even when using the "Drupal Default Editor" option, involves when text processing occurs and what data is stored. Drupal does not (generally) process your input before persisting that to the database. Instead it saves your raw input to the database and applies input formats (which consist of input filters that actually modify text) when text is **output**. This is very important because many input filters with advanced functionality may rely on using special tags in the raw input that are translated into proper HTML only on output.

CiviCRM doesn't know anything about input formats. There is no single point in the CiviCRM code where we can apply text processing for any WYSIWYG related fields just prior to output. Nor is there any practical way to store what input format has been used to work on a field. As a result, the Drupal Default Editor for CiviCRM applies input formats **before** saving your input to the database. This means you get the output you expect, however it means you **can't go back**. If you edit a record with a WYSIWYG form for which you've previously changed the input format, CiviCRM won't know that, and you'll start out with the default editor. Also, advanced filters and WYSIWYG buttons that rely on manipulating template information in the raw HTML will not work, since only the processed output is stored.

This effect will be especially evident if you use an input format such as the default Filtered HTML without a WYSIWYG profile assigned for a field. In native Drupal, this is basically just a non-WYSIWYG text editor -- you edit as plain text, optionally add some HTML, and line breaks, links and other doo-dads get converted to proper HTML when it's displayed. When you return to the field in CiviCRM after navigating away, it will:

1. have the default input format chosen in the CiviCRM preferences screen selected regardless of which input format you may have previously selected, and
1. even when Filtered HTML is selected, it will display HTML in the non-WYSIWYG text area! This is because input formats have been applied to the raw code, newlines were converted to paragraphs, links were created, etc.

## IMCE as a File Browser

If you have configured WYSIWYG profiles to use IMCE as a file browser, those will work exactly as you expect when editing rich text fields in CiviCRM. Hooray! You'll need [IMCE](http://drupal.org/project/IMCE) and [IMCE WYSIWYG bridge](http://drupal.org/project/imce_wysiwyg) installed of course. See the README file in IMCE WYSIWYG bridge for installation instructions.

!!! tip

    Using Drupal Default Editor along with an IMCE WYSIWYG profile is currently the only way to allow users to browse, upload and otherwise manage files on your server for use with mailings, event descriptions, etc.


##
 Absolute Paths

When using this option for CiviMail in particular you'll need to take special care to ensure absolute URLs are output. I strongly recommend the [pathologic](http://drupal.org/project/pathologic) module to help with this task. As a best practice, consider creating an input format specifically for use with CiviMail, as it has special considerations one must account for. Be sure that pathologic is applied as an input filter for the format (see the pathologic documentation for details). One additional step is required to ensure it treats all resources as local and properly converts them to absolute URLs. Go to the input formats screen (admin/settings/filters/list), click "configure" on the format, then click the "configure" tab. In the settings for the Pathologic filter, you'll need to enter your site's base path in the "Also considered local" textbox. If your site is in the root, this will just be "/" (no quotes). If your site is in a subdirectory, such as [http://www.example.com/drupal](http://www.example.com/drupal), you'll need to enter "/drupal".