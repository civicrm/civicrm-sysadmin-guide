# Moneris Configuration Guide

## Overview

Moneris is a Canadian transaction processor, working with most of the major credit cards and now direct payment cards as well. There is a US version of the Moneris API out there, but it hasn't been implemented yet.

Unfortunately, they haven't given us an appropriate GPL license, so as of version 3.1.4, there is some assembly required.

Specifically, you'll have to get a recent version of the mgpClasses.php file that they include with their PHP API. Once you've obtained that file, you'll then need to modify it slightly to get it to respect the CiviCRM payment processor configuration. To obtain the latest version of mpgClasses.php, you'll need a developer account with Moneris here: [https://developer.moneris.com/](https://developer.moneris.com/). The registration goes through an approval process. In my case, it took one business day to have my account verified. Once you have your account, login and find the API Integration Method, then go to the Basic Transaction Functions page. On that page, download the PHP Transaction API. This is a ZIP file that contains the file that we are looking for, mpgClasses.php. Also handy is the PHP Transaction Guide that is on the same page available for download. While most of these details are irrelevant, there is information about using the Moneris test environment and test card numbers you can use that are essential to ensuring your instance of CiviCRM is working together with the Moneris API.

Once you have the mpgClasses.php file, put it in the packages/Services folder. There are now a couple edits needed to make the API respect the CiviCRM payment processor settings. [Download a patch file of these edits](http://matthewoliveira.com/sites/default/files/).

## Additional notes

1. You don't actually need to get a Moneris merchant account to test it out, but obviously for production you do. The Moneris test environment is located at [https://esqa.moneris.com/](https://esqa.moneris.com/). The credentials and instructions for logging in are on that page. When you use your contributions pages in 'Test Drive' mode, the transactions will be recorded here. Note that this test environment is shared by all developers testing the Moneris API, so don't send through any personal data.
1. The Moneris plugsin requires the default currency above to be set to 'CAD'.
1. You'll need to put in your Store ID and API token as recieved from Moneris in the CiviCRM admin, but the store2 (use store3 for recurring payments) and yesguy strings in the Test section are real valid entries for the Moneris test environment (there are 2 other allowable ones, see their documentation if you want to use a different test store). The test values won't work in production, it uses a different domain.
1. An noted above, it's important to understand that the results of the test environment are shared with other developers, i.e., only use test info, others can see what you post.
1. Another key difference from PayPal is that the test environment is setup to give various error codes depending on the dollar amount you send. See their PHP Transaction Guide if you want to test what happens when someone submits a payment with a maxxed out card, for example, or to simulate the processor being unavailable.
1. Moneris provides a web interface to do lots of administrative stuff (e.g. reversing charges, managing recurring payments), not currently handled by CiviContribute. Lots of contributor information is also sent to Moneris to help you match up data in your accounting.
1. Recurring payments, while functional, is incomplete. The Moneris payment processor in CiviCRM will trigger recurring transactions in Moneris, however that is about it. There is no way, out of the box, for CiviCRM to poll or be notified when a recurrence happens and log the respective transaction as a contribution. As such, you will need to manually monitor your Moneris admin for recurring transactions, and when they occur, you'll need to manually enter the contribution on the CiviCRM admin in order to keep the data in CiviCRM up to date. There is also no way that I am aware of to manage the Moneris end of a recurring contribution. For cancelations, credit card information updates and otherwise, you'll need to use your Moneris admin account. This of course leaves much to be desired, so take that into consideration before choosing Moneris as your payment processor for CiviCRM.

## Setting up Moneris Hosted Payment Processor for CiviCRM

This document serves as a brief description of how you go about setting up Moneris Hosted to work with CiviCRM 4.x

1. The first thing is configure and Install Moneris extension in CiviCRM extensions. ([http://wiki.civicrm.org/confluence/display/CRMDOC40/Extensions+Admin](http://wiki.civicrm.org/confluence/display/CRMDOC40/Extensions+Admin))
1. You should now be able to view the following screen when you set up extension for Moneris Hosted Payment Processor.
    ![](https://wiki.civicrm.org/confluence/download/attachments/86213583/Screenshot%20from%202013-05-20%2020%3A56%3A21.png?version=1&modificationDate=1372586539000&api=v2)

1. The next thing is to configure Moneris eSELECT Plus Hosted Paypage . The following configuration steps are required under your Moneris eSELECT Plus merchant account.Please ensure you have your merchant account set up on moneris before continuing.

1. Log into the Merchant Resource Center: [Developement](https://esqa.moneris.com/mpg/index.php) [Production](https://www3.moneris.com/mpg)

    Before you can send a transaction to the Hosted Paypage you will need to configure several settings through the eSELECTplus Merchant Resource Centre (MRC).
     To log into the MRC test environment go to [https://esqa.moneris.com/mpg](https://esqa.moneris.com/mpg) and use one of the following login IDs.
    
    | Store ID | Username | Password |
    | --- | --- | --- |
    | store1 | demouser | password |
    | store2 | demouser | password |
    | store3 | demouser | password |

    1. Navigate to Admin -> Hosted Config
    
        Once you have successfully logged in, click on the “ADMIN” menu item on the left and then in the submenu that appears click on “HOSTED CONFIG”.
        
        To create a new Hosted Paypage configuration, click on “Generate a New Configuration”. You will be assigned a Hosted Paypage ID (ps_store_id). This is the identifier for this unique configuration. You will also be assigned a Hosted Paypage Token (hpp_key). The Hosted Paypage ID and Token are sent as part of the transaction request to securely identify your store and the specific configuration.
    
        ![](https://wiki.civicrm.org/confluence/download/attachments/86213583/Screenshot%20from%202013-05-17%2014%3A10%3A39.png?version=1&modificationDate=1372586539000&api=v2)
    
    1. Set Transaction Type to Purchase (default).
    1. Under Payment Methods, ensure Credit Cards is enabled, all others disabled.
    1. Set Response Method to Sent to your server as a POST.
    1. Set Approved URL and Declined URL to: https://<YOUR HOST>/civicrm/payment/ipn?processor_name=MonerisEselect
    
        ![](https://wiki.civicrm.org/confluence/download/attachments/86213583/Screenshot%20from%202013-05-20%2020%3A49%3A26.png?version=1&modificationDate=1372586539000&api=v2)
    
    1. Choose "Use Enhanced Cancel"  if you want to return a full response to decline url.
    
    1. Click the Save Changes button.
    1. Click the Configuration Appearance button under Paypage Appearance.
    1. Set Cancel Button Text:
    
        ![](https://wiki.civicrm.org/confluence/download/attachments/86213583/Screenshot%20from%202013-05-20%2020%3A49%3A41.png?version=1&modificationDate=1372586539000&api=v2)
    
    1. Click the Save Appearance Settings button then Return to main configuration.
    1. Enable Return the txn_number. This field is used to perform follow-ons and leave all others disabled.
    1. Click the Save Response Settings button then Return to main configuration.
    1. Click the Configuration Security button under Security Features.
    1. Check Enable Transaction Verification and select Displayed as xml on our server.
    
        ![](https://wiki.civicrm.org/confluence/download/attachments/86213583/Screenshot%20from%202013-05-17%2016%3A14%3A31.png?version=1&modificationDate=1372586539000&api=v2)
    
        Response URL : https://<YOUR HOST>/civicrm/payment/ipn?processor_name=MonerisEselect
    
    1. Click the Save Verification Settings button then Return to main configuration.

1. The Site URLs namely [Production](https://www3.moneris.com/) [Test](https://esqa.moneris.com/) serve as a means of communication between CiviCRM and Moneris Hosted. Your CiviCRM Payment Processor screen should look something like this:

    ![](https://wiki.civicrm.org/confluence/download/attachments/86213583/Screenshot%20from%202013-05-20%2020%3A56%3A53.png?version=1&modificationDate=1372586539000&api=v2)
