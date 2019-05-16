# Payment Processors

CiviContribute can be used to record and manage payments accepted through channels outside of CiviCRM, such as checks, money orders, cash, and PoS terminals. However, it is very convenient to allow users to make payments directly through CiviCRM pages for donations, memberships, and events. This requires the organization to get an account with payment processor, and to configure a CiviCRM payment processor plugin to connect with your account at the payment processor. Once setup, you likely will not need to change the configuration again.

Plugins are available to enable CiviCRM to process payments directly via several payment processors. Their features, the countries they are available in, their costs, their ease of use by your users, and whether you need a Merchant account a bank or an SSL certificate for you website vary by payment processor. This information is tabulated below for your reference.

!!! note "SEPA for the European Union countries that use SEPA"

    If you need to do anything with payments in the European Union you are likely to come across [SEPA](https://en.wikipedia.org/wiki/Single_Euro_Payments_Area). If you need to deal with SEPA payments, there is an extension that supports this: [CiviSepa](https://civicrm.org/extensions/civisepa-sepa-direct-debit-extension)

## Setup and Configuration

To setup a new payment processor, click on Administer > System Settings > Payment Processors, Add Payment Processor.

Select a Payment Processor Type that corresponds to the payment processor you have setup an account with after referring to the [comparison chart](#comparison) below. Provide a name for the payment processor that will be used to enable the payment processor on Contribution and Event Pages in your system. Select a Financial Account that your bookkeeping system uses to track monies in the payment processor account. Usually you will want to have a separate Financial Account for each payment processor.

Optionally provide a description for the payment processor, and indicate if the payment processor should be Enabled, which makes it available for use on Contribution and Event pages. For convenience in configuring those pages, you can indicate that one payment processor is the Default.

## Comparison

### Chart

| Processor Name                                                   | In        | Type     | Recurring? | Countries  | Merchant? |
| ---                                                              | ---       | ---      | ---        | ---        | ---       |
| Dummy (for testing only)                                         | Core      |          |            |            |           |
| [Authorize.net](/setup/payment-processors/authorize-net.md)      | Core      | SSL      | Yes        | Many       | Yes       |
| [PayPal_Standard](/setup/payment-processors/paypal-standard.md)  | Core      | External | Yes        | Many       | No        |
| [PayPal Pro](/setup/payment-processors/paypal-pro.md)            | Core      | SSL      | Yes        | Many       |           |
| [Paypal Express](/setup/payment-processors/paypal-pro.md)        | Core      |          | Partial    | Many       |           |
| [Card Access](/setup/payment-processors/card-access.md)          | Extension |          |            |            |           |
| [Moneris](/setup/payment-processors/moneris.md)                  | Extension | SSL      |            |            |           |
| [PayJunction](/setup/payment-processors/payjunction.md)          | Extension | SSL      |            |            |           |
| [iATS Payments](/setup/payment-processors/iats.md)               | Extension | SSL      | Yes        | Many       | No        |
| Payment Express (DPS)                                            | Extension | External | Yes        | Many       | Yes       |
| [Ogone](/setup/payment-processors/ogone.md)                      | Extension |          |            |            | No        |
| eWay                                                             | Extension | SSL      | Yes        | AU         |           |
| [First Data](/setup/payment-processors/first-data.md)            | Extension | SSL      | No         |            |           |
| Elavon / Nova                                                    | Extension | SSL      | No         |            |           |
| World Pay                                                        | Extension |          |            |            |           |
| Sogenactif                                                       | Extension | External |            |            |           |
| eProcessing Network                                              | Extension | SSL      | No         |            | Yes       |
| Quickbooks Merchant Services                                     | Extension | SSL      | No         |            | Yes       |
| [Payflow Pro](/setup/payment-processors/payflow.md)              | Extension | SSL      | No         |            |           |
| Barclays                                                         | Extension | SSL      |            |            |           |
| Card Access Services                                             | Extension | External | No         | AU         | Yes       |
| Vanco                                                            | Extension | SSL      | Yes        |            | Yes       |
| Stripe                                                           | Extension | SSL      | Yes        | US, CA, UK | Yes       |
| clickandpledge                                                   | Extension | SSL      | No         | US         | No        |

### Key

* **Type**
    * SSL - Users submit their credit card directly on your site
    * External - Users are redirected to another site when submitting their credit card

* **Merchant** - Does using this payment processor require a merchant account?

* **Recurring** - Is this payment processor capable of handling recurring donations and auto-renewing memberships?
    * PayPal Express: Users can create recurring contributions, but subsequent contributions must be entered manually into CiviCRM. 3rd party script providing automation of this process may be available from Koumbit.org
    * Payment Express (DPS): No, Payment Express can do 'triggered' recurring transations but there is no CiviCRM support for this
    * eWay: Code written but needs further customisation, see [work in progress](https://github.com/henare/CiviCRM-eWay-recurring-payment-processor)


### Notes

* **Authorize.net**: Recurring contributions require ARB support, which is an additional monthly fee. Possibly cheaper through a re-seller than direct. Only AIM and ARB methods (not SIM) are supported.

* **PayPal_Standard**: Accepted Paypal payments too. Can be confusing for users who only want to pay by credit cards. High abandon rate There is a delay in withdrawing funds of 3-5 days and withdrawals need to be manually actioned

* **PayPal Pro**: Recurring contributions require not only a fee to upgrade to "Pro" but an additional fee for the Recurring add-on feature. Paypal only accepts recurring payments through Credit Cards. Paypal does not process recurring ACH payments. NOTE: New PayPal Pro accounts require you to use PayFlow for recurring payments. PayPal intends to migrate all PayPal Pro accounts to use Payflow at some future point. I signed up for Paypal Websites Pro on 8/12/15 and they included recurring payments free with $30 monthly fee. Very confusing offerings.

* **iATS Payments**: iATS Payments only services non-profits and gets a better rate because of that. They have separate North American and UK based servers. Support for ACH/EFT, Recurring contributions using tokens, UK DIrect Debit, and swipe devices. Active development and support from iATS themselves for the extension. <http://home.iatspayments.com/> <https://civicrm.org/extensions/iats-payments> <https://civicrm.org/extensions/iats-payments> Community contributed (Alan Dixon, Karin Gerritsen) 

* **Payment Express (DPS)**: This option is a good one if you do a lot of transactions (e.g. $1000-$2000 per month) You can expect to pay a monthly rate for this and possibly a monthy fee to DPS - depending on your bank's package. If you are in New Zealand Westpac sells this as its WIPS plus product. At time of writing this was cheaper than the other banks.We found that although they told us we needed to have other accounts with Westpac this was primarily a credibility issue and they were prepared to trust us based on our personal history with them. Both pxaccess & pxpay methods are supported. <http://www.paymentexpress.com/index.html> Community contributed (Eileen & Lucas Baker) 

* **eWay**: In Core Community Contributed (Peter Barwell of Dolphin Software, Henare Degan, Chris Ward) 

* **First Data**: This code is still being tested and is not in the Core yet Community Contribution (Eileen) 

* **Elavon / Nova**: In Core Community Contribution (Eileen) 

* **World Pay**: Search Forums for latest. Code has been written but not ?yet? available to community ? 

* **Sogenactif**: Code under Development Community Contribution (Eileen) 

* **eProcessing Network**: Offers emulation of Authorize.Net<sup>TM</sup>. Does not support recurring payments using Emulation mode. [http://www.eprocessingnetwork.com/Utilities.html](http://www.eprocessingnetwork.com/Utilities.html) 

* **Quickbooks Merchant Services**: In Development. Check back soon! Contact us for collaboration or funding. Community Contributed (Shawn) 

* **Payflow Pro**: Contributed Community Contribution (Eileen) 

* **Barclays**: Contributed Community Contribution (Eileen) 

* **Card Access Services**: PCI compliant hosted payment transaction processing [http://developer.cardaccess.com.au/en/civicrm-integration/](http://developer.cardaccess.com.au/en/civicrm-integration/) Card Access Services 

* **Vanco**: Requires Drupal module. Includes ACH (electronic check) capabilities [https://drupal.org/project/vanco_payment](https://drupal.org/project/vanco_payment) BackOffice Thinking 

* **Stripe**: Extremely fast & easy merchant account setup. All you need is an email address & bank account you want the money to go to. PCI compliance is a breeze because card numbers aren't used. Stripe uses javascript which 'tokenizes' the card data just before form submission. Provider: [https://stripe.com/](https://stripe.com/) Developer: [http://drastikbydesign.com/](http://drastikbydesign.com/) Community Contributed (drastik-jw) 

* **clickandpledge**: Provided by clickandpledge [https://github.com/ClickandPledge](https://github.com/ClickandPledge) 


!!! note "Configuring SSL for Your Online Contribution Pages"

    If you select a payment processor configuration where users are entering their credit card information in a form on your site (such as PayPal Pro, Authorize.net, PayJunction or Moneris), you will also need to configure your web-server and CivCRM to support SSL (secure encrypted) pages.

    Check with your server administrator or hosting service for information on obtaining an SSL certificate and configuring your web server to support SSL encrypted pages (more info...CRM:SSL Configuration for Apache and CiviCRM].

    Once you have verified that you can access pages in your site using _[http://example.org/_](http://example.org/_) then configure CiviCRM to either use SSL for all pages (this is more secure) or at least redirect all online contribution pages to use SSL:

    * Navigate to **Administer » System Settings » Resource URLs**
    * Set **Force Secure URLs (SSL)** to YES and save your change.
    * Verify the new setting by clicking **Test-drive** or **Live Page** from **Administer » CiviContribute »**  **Manage Contribution Pages**. Your contribution forms should automatically redirect to HTTPS and you should see the encryption (lock) symbol in your browser.

!!! note

    **NB:**  **For the highest security when not using externally hosted payment processors, it is best to deploy your site using https only** , rather than redirecting from http pages to https for contribution pages (see [http://drupal.org/https-information](http://drupal.org/https-information)).


## Processor Configuration Guides

To set up the payment processor always start with a new entry. Don't try to change a payment processor setup from one setup (i.e. PayPal standard) to another (i.e. PayPal Pro). Starting with a new entry will ensure that all fields are filled with default values. A simple change will NOT fill in default values. This might lead to invalid URL's beeing used etc.. Error messages in this case are very thin, down to nothing.

Note that if you are doing a test-drive transaction, e.g. using the paypal sandbox, then test contributions and memberships do NOT show up on the contact's contribution and membership tabs. You can find / verify them from the Find Memberships and Find Contributions menu items - check the "Find Test ..." checkbox in the search forms.

If you are experiencing problems, go to the demo site and try to replicate your problem there. If its working there, the chances are that the parameters you filled in for your payment processor are not correct.
