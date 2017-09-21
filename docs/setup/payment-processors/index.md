# Payment Processors

* Overview
* Setup and Configuration
* Processor Comparison Chart
* Processor Configuration Guides

* Authorize.net Configuration
* First Data / Linkpoint / Global Gateway
* PayJunction Configuration
* PayPal Website Payments Pro and Express Configuration
* PayPal Website Payments Standard and Recurring Contributions
* iATS Payments Configuration
* eProcessing Network Configuration
* PayFlow Link
* Card Access Services CASConnect Configuration

# Overview

CiviContribute can be used to record and manage payments accepted through channels outside of CiviCRM, such as checks, money orders, cash, and PoS terminals. However, it is very convenient to allow users to make payments directly through CiviCRM pages for donations, memberships, and events. This requires the organization to get an account with payment processor, and to configure a CiviCRM payment processor plugin to connect with your account at the payment processor. Once setup, you likely will not need to change the configuration again.

Plugins are available to enable CiviCRM to process payments directly via several payment processors. Their features, the countries they are available in, their costs, their ease of use by your users, and whether you need a Merchant account a bank or an SSL certificate for you website vary by payment processor. This information is tabulated below for your reference.

!!! note "SEPA for the European Union countries that use SEPA"

    If you need to do anything with payments in the European Union you are likely to come across [SEPA](https://en.wikipedia.org/wiki/Single_Euro_Payments_Area). If you need to deal with SEPA payments, there is an extension that supports this: [CiviSepa](https://civicrm.org/extensions/civisepa-sepa-direct-debit-extension)


# Setup and Configuration

To setup a new payment processor, click on Administer > System Settings > Payment Processors, Add Payment Processor.

Select a Payment Processor Type that corresponds to the payment processor you have setup an account with after referring to the [Processor Comparison Chart](http://wiki.civicrm.org/confluence/display/CRMDOC43/Payment+Processors#PaymentProcessors-ProcessorComparisonChart) below. Provide a name for the payment processor that will be used to enable the payment processor on Contribution and Event Pages in your system. Select a Financial Account that your bookkeeping system uses to track monies in the payment processor account. Usually you will want to have a separate Financial Account for each payment processor.

Optionally provide a description for the payment processor, and indicate if the payment processor should be Enabled, which makes it available for use on Contribution and Event pages. For convenience in configuring those pages, you can indicate that one payment processor is the Default.

The remaining fields to be setup on this form vary by Payment Processor. Refer to the links in the individual [Processor Configuration Guides](http://wiki.civicrm.org/confluence/display/CRMDOC43/Payment+Processors#PaymentProcessors-ProcessorConfigurationGuides) below for setup instructions for your specific payment processor type.

# Processor Comparison Chart

See [http://book.civicrm.org/user/contributions/payment-processors](http://book.civicrm.org/user/contributions/payment-processors) for an overview of things to consider when selecting a processor.

| Processor Name | Type (externally hosting/
 SSL on site) | Recurring
 contributions / Auto-renew Memberships | Regional
 Availability | Merchant
 account required | Comments | Website | Contributed/
 supported by? |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Dummy | | | | | For testing | | |
| Authorize.net | SSL | Yes | | Yes | Recurring contributions require ARB support, which is an additional monthly fee.

 Possibly cheaper through a re-seller than direct (unconfirmed rumour). Only AIM and ARB methods (not SIM) are supported. | | CiviCRM Core Team (as of v3.3.2). |
| PayPal_Standard | External | Yes | Multi currency - supports just about everywhere | No | Accepted Paypal payments too.
 Can be confusing for users who only want to pay by credit cards. High abandon rate
 There is a delay in withdrawing funds of
 3-5 days and withdrawals need to be manually actioned | | CiviCRM core team |
| PayPal Pro | SSL | Yes | Multi currency - supports just about everywhere | | Recurring contributions require not only a fee to upgrade to "Pro" but an additional fee for the Recurring add-on feature. Paypal only accepts recurring payments through Credit Cards. Paypal does not process recurring ACH payments.
 NOTE: New PayPal Pro accounts require you to use PayFlow for recurring payments. PayPal intends to migrate all PayPal Pro accounts to use Payflow at some future point. I signed up for Paypal Websites Pro on 8/12/15 and they included recurring payments free with $30 monthly fee. Very confusing offerings. | | CiviCRM core team |
| Paypal Express | SSL on site or paypal off-site? | | Multi currency - supports just about everywhere | | | | CiviCRM core team |
| Moneris | SSL | Users can create recurring contributions, but subsequent contributions must be entered manually into CiviCRM. 3rd party script providing automation of this process may be available from Koumbit.org | | | | | Community contributed |
| PayJunction | SSL | | | | | | |
| iATS Payments | SSL | Yes | CA, US, AU, UK, Euros, NZ | No | iATS Payments only services non-profits and gets a better rate because of that. They have separate North American and UK based servers. Support for ACH/EFT, Recurring contributions using tokens, UK DIrect Debit, and swipe devices. Active development and support from iATS themselves for the extension. | [[http://home.iatspayments.com/](http://home.iatspayments.com/)]

 [[https://civicrm.org/extensions/iats-payments](https://civicrm.org/extensions/iats-payments)] | Community contributed (Alan Dixon, Karin Gerritsen) |
| Payment Express
 (DPS) | External | No, Payment Express
 can do 'triggered' recurring transations but
 there is no CiviCRM support for this | NZ - this is the most significant card processor in NZ
 DPS are certified with banks in [Australia](http://www.paymentexpress.com.au/), [New Zealand](http://www.paymentexpress.com/about/connectivity/new_zealand.html), [Pacific Islands](http://www.paymentexpress.com/about/connectivity/pacific_islands.html), [Singapore](http://www.paymentexpress.com/about/connectivity/singapore.html), [South Africa](http://www.paymentexpress.com/about/connectivity/south_africa.html), [USA](http://www.paymentexpress.com/about/connectivity/usa.html) and [United Kingdom](http://www.paymentexpress.com/about/connectivity/united_kingdom.html). | Yes | This option is a good one if you do a lot of transactions (e.g. $1000-$2000 per month)
 You can expect to pay a monthly rate for this and possibly a monthy fee to DPS - depending on your bank's package.

 If you are in New Zealand Westpac sells this as its WIPS plus product. At time of writing this was cheaper than the other banks.We found that although they told us we needed to have other accounts with Westpac this was primarily a credibility issue and they were prepared to trust us based on our personal history with them.
 Both pxaccess & pxpay methods are supported. | [http://www.paymentexpress.com/index.html
 ] | Community contributed (Eileen & Lucas Baker) |
| Paymex | External | No | NZ | No | Good kiwi option for low volume. 3.25% & no set up fees but you do need to withdraw your money manually | | Paymex have written the code and it is available from their site |
| eWay | SSL | Code written but needs further customisation, see [work in progress](https://github.com/henare/CiviCRM-eWay-recurring-payment-processor) | Australia + ?? | ? | In Core | | Community Contributed (Peter Barwell of Dolphin Software, Henare Degan, Chris Ward) |
| First Data /
 Global Gateway | SSL | No code written but processor can support it | ? | ? | This code is still being tested and is not in the Core yet | | Community Contribution (Eileen) |
| Elavon / Nova | SSL | No code written but processor can support it | | | In Core | | Community Contribution (Eileen) |
| World Pay | | | | | Search Forums for latest. Code has been written but not ?yet? available to community | | ? |
| Sogenactif | External | | | | Code under Development | | Community Contribution (Eileen) |
| eProcessing Network | SSL | No code written but processor can support it | | Yes | Offers emulation of Authorize.Net<sup>TM</sup>. Does not support recurring payments using Emulation mode. | [http://www.eprocessingnetwork.com/Utilities.html](http://www.eprocessingnetwork.com/Utilities.html) | |
| Quickbooks Merchant Services | SSL | Not yet | | Yes | In Development. Check back soon! Contact us for collaboration or funding. | | Community Contributed (Shawn) |
| Payflow Pro | SSL | No code written but processor can support it | | | Contributed | | Community Contribution (Eileen) |
| Barclays | SSL | | | | Contributed | | Community Contribution (Eileen) |
| Card Access Services | External | No code written but processor can support it | Australia + many banks worldwide | Yes | PCI compliant hosted payment transaction processing | [http://developer.cardaccess.com.au/en/civicrm-integration/](http://developer.cardaccess.com.au/en/civicrm-integration/) | Card Access Services |
| Vanco | SSL | Yes | | Yes | Requires Drupal module. Includes ACH (electronic check) capabilities | [https://drupal.org/project/vanco_payment](https://drupal.org/project/vanco_payment) | BackOffice Thinking |
| Stripe | SSL | Yes | United States, Canada, the UK, Ireland | Yes | Extremely fast & easy merchant account setup. All you need is an email address & bank account you want the money to go to. PCI compliance is a breeze because card numbers aren't used. Stripe uses javascript which 'tokenizes' the card data just before form submission. | Provider:
[https://stripe.com/](https://stripe.com/) Developer:
[http://drastikbydesign.com/](http://drastikbydesign.com/) | Community Contributed
 (drastik-jw) |
| clickandpledge | SSL | No | United States and ? | No | Provided by clickandpledge | [https://github.com/ClickandPledge](https://github.com/ClickandPledge) | |

*Note that 'triggered recurring' refers to where a processor retains the authorization on the credit card and will recharge it on request. This is not currently implemented in civiCRM.

Click on the appropriate link below for information on set-up for your processor of choice.

This page will be updated as support for additional processors becomes available. If you are interested in developing a plug-in for another processor - review [this page](https://wiki.civicrm.org/confluence/display/CRMDOC/Create+a+Payment-Processor+Extension). Then contact the CiviCRM development team at civicrm-dev@lists.civicrm.org if you need further guidance.

!!! note "Configuring SSL for Your Online Contribution Pages"

    If you select a payment processor configuration where users are entering their credit card information in a form on your site (such as PayPal Pro, Authorize.net, PayJunction or Moneris), you will also need to configure your web-server and CivCRM to support SSL (secure encrypted) pages.

    Check with your server administrator or hosting service for information on obtaining an SSL certificate and configuring your web server to support SSL encrypted pages (more info...|CRM:SSL Configuration for Apache and CiviCRM].

    Once you have verified that you can access pages in your site using _[https://yoursite.org/_](https://yoursite.org/_) then configure CiviCRM to either use SSL for all pages (this is more secure) or at least redirect all online contribution pages to use SSL:

    * Navigate to **Administer » System Settings » Resource URLs**
    * Set **Force Secure URLs (SSL)** to YES and save your change.
    * Verify the new setting by clicking **Test-drive** or **Live Page** from **Administer » CiviContribute »**  **Manage Contribution Pages**. Your contribution forms should automatically redirect to https://... and you should see the encryption (lock) symbol in your browser.

!!! note

    **NB:**  **For the highest security when not using externally hosted payment processors, it is best to deploy your site using https only** , rather than redirecting from http pages to https for contribution pages (see [http://drupal.org/https-information](http://drupal.org/https-information)).


# Processor Configuration Guides

### [Authorize.net Configuration](https://wiki.civicrm.org/confluence/display/CRMDOC/Authorize.net+Configuration)

### [First Data / Linkpoint / Global Gateway](https://wiki.civicrm.org/confluence/display/CRMDOC33/First+Data%2C+Linkpoint%2C+Global+Gateway)

### [PayJunction Configuration](https://wiki.civicrm.org/confluence/display/CRMDOC/PayJunction+Configuration)

### [PayPal Website Payments Pro and Express Configuration](https://wiki.civicrm.org/confluence/display/CRMDOC/PayPal+Website+Payments+Pro+and+Express+Configuration)

### [PayPal Website Payments Standard and Recurring Contributions](https://wiki.civicrm.org/confluence/display/CRMDOC/PayPal+Website+Payments+Standard+and+Recurring+Contributions)

### [iATS Payments Configuration](https://wiki.civicrm.org/confluence/display/CRMDOC/iATS+Payments+Configuration)

### [eProcessing Network Configuration](http://wiki.civicrm.org/confluence/display/CRMDOC/eProcessing+Network+Configuration+%28via+Authorize.net+Emulation%29 "eProcessing Network Configuration (via Authorize.net Emulation)")

### [PayFlow Link](https://wiki.civicrm.org/confluence/display/CRMDOC/PayFlow+Link)

### [Card Access Services CASConnect Configuration](https://wiki.civicrm.org/confluence/display/CRMDOC/Card+Access+Services+CASConnect+Configuration)# Trouble shooting payment processor setup

To set up the payment processor always start with a new entry. Don't try to change a payment processor setup from one setup (i.e. PayPal standard) to another (i.e. PayPal Pro). Starting with a new entry will ensure that all fields are filled with default values. A simple change will NOT fill in default values. This might lead to invalid URL's beeing used etc.. Error messages in this case are very thin, down to nothing.

Note that if you are doing a test-drive transaction, e.g. using the paypal sandbox, then test contributions and memberships do NOT show up on the contact's contribution and membership tabs. You can find / verify them from the Find Memberships and Find Contributions menu items - check the "Find Test ..." checkbox in the search forms.

If you are experiencing problems, go to the demo site and try to replicate your problem there. If its working there, the chances are that the parameters you filled in for your payment processor are not correct.