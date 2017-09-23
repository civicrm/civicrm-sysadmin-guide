# Card Access Services CASConnect Configuration

## Download Link

This is available from the download section in [http://developer.cardaccess.com.au/en/civicrm-integration/#download](http://developer.cardaccess.com.au/en/civicrm-integration/#download)

## Installation & Configuration

### Ensure your CiviCRM extensions directory is configured

More details can be found on the CiviCRM website, but typically this involves logging in as administrator and editing the directory setting:

* CiviCRM 3.4.8 and 4.0.8: "Administer->Configure->Global Settings->Directories->CiviCRM Extensions Directory"
* CiviCRM 4.1: "Administer->System Settings->Directories->CiviCRM Extensions Directory"

If you have already configured other extensions then this step is not necessary

### Install extension

This requires 3 separate steps

#### Unpack extension

Unzip au.com.cardaccess.payment.casconnect.zip into the extensions directory (as configured above)

#### Copy the IPN files to their final location

Please copy the following files into CiviCRM's "extern" directory:

1. cASConnectNotify.php
1. CASConnectIPN.php
1. CASConnectUtil.php

The "extern" directory varies according to the CRM hosting CiviCRM, for instance:

* Joomla 1.7.4: [JOOMLA_DIRECTORY]/administrator/components/com_civicrm/civicrm/extern
* Drupal 7.12: [DRUPAL_DIRECTORY]/sites/all/modules/civicrm/extern

This above procedure is the recommended way as it will work without further modifications

But if you absolutely cannot modify the extern directory (e.g. your hosting provider won't allow it) and need to use another URL then this can be done but it will require some manual fiddling - please contact us for more details, we will need to make a configuration change to your account on our end and the notify script

#### Activate the CASConnect Payment Processor

Please do the following:

* Log into your website as an administrator and navigate to the "CiviCRM Extensions" setting
    * CiviCRM 3.4.8 and 4.0.8: "Administer->Customize->Manage CiviCRM Extensions"
    * CiviCRM 4.1: "Administer->Customise Data And Screens->Manage CiviCRM Extensions"
* You should now see CASConnect become available for installation. Click "Install" and then "Install" again

### Configure extension

Please configure the following items before starting to process transactions (these will be randomly generated when an account is setup on our end):

* Merchant ID
* Transaction Password
* Callback Password

In some cases you might also be provided an aggregator ID. If this is not provided then the aggregator ID field can be left blank

It is recommended that the other fields be left as is (especially the Site and Button URLs)
