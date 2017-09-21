# Set-up

In this chapter, the steps required to set up an SMS gateway will be
explored. Once configured, you will be able to send text messages to
individual contacts and mass mailing lists within CiviCRM.

## Configuring a Clickatell SMS Gateway

### Registering for a Clickatell account

The Clickatell service is an online SMS gateway designed for the sending
of text messages via the Internet. They offer several products, but here
we are only interested in the "Developer's Central" SMS gateway, as it
offers more advanced tools needed to link it to CiviCRM (this is
achieved through APIs; see the CiviCRM developer guide for more
information). Registering for a Clickatell account is free, and they
supply 10 complimentary credits to try the service. To sign up, visit:

[http://www.clickatell.com/register/](http://www.clickatell.com/register/)

Once you have registered for a Developer's Central account, please
sign-in and follow the steps below (when logging in you must select
"Central API" as the product and enter your Client ID):

1.
In the Central Home dashboard, click "Create a new Connection" under
"Connection Status" (New language is "Get another API". Page looks also changed.)

![Clickatell settings screen](../img/clickatell-settings.png)

1.
Select "HTTP/S" as the connection type (New language: HTTP API)

1.
Four optional settings will appear, including:
    -   Description: change the name of the connection (e.g. "CiviCRM HTTP")
    -   Replace leading zero: enable this option if phone numbers against
        your contacts begin with "0". For delivery to be successful, all
        numbers must begin with the country code if this is not enabled.
        (This option no longer exists.)
    -   Enable IP Address Restriction: if you know the IP address of your
        CiviCRM server, you can enter it here to ensure that text messages
        cannot be sent using your account elsewhere (your username and
        password would be needed to do this)
        (New language:  "Protect your account from fraud by restricting IP addresses")
    -   Enable your app to receive message delivery notifications (New option?)
        Enter the correct URL for your site. Example:
        Drupal: http://www.example.com/civicrm/sms/callback?provider=org.civicrm.sms.clickatell
        Wordpress: https://www.example.org/?page=CiviCRM&q=civicrm%2Fsms%2Fcallback&provider=org.civicrm.sms.clickatell



1.
Click "Submit and Get API ID" to generate an API ID, and on the next
page, make a note of it.

### Completing the SMS Provider settings in CiviCRM

You now have all of the information needed to configure SMS in CiviCRM.
To continue, return to CiviCRM and go to: **Administer** > **System
Settings**> **SMS Providers**. Click "Add New Provider".

![image](../img/CiviCRM_SMS_adding-a-provider.png)

Complete the following settings:

-   Name: select "Clickatell"
-   Title: give the SMS provider a title user's will see (e.g.
    "Clickatell SMS")
-   Username: enter your Clickatell username
-   Password: and your Clickatell password
-   API type: select "http"
-   API URL: type the URL as follows: https://api.clickatell.com
-   API Parameters: this is where you should provide your API ID. The
    format required is: `api_id=8473658`
-   Is this provider active?: tick to enable the SMS gateway
-   Is this a default provider?: check this option to make it the
    default, where multiple SMS providers are available

CiviCRM will now be configured to send text messages to your contacts.

### Testing the SMS gateway

You can begin testing the gateway using the methods laid out in the
chapter "Everyday tasks". However, please note that if you are using the
10 complimentary SMS credits which came with the account, until you have
purchased credits Clickatell will replace the content with thank you
text like the message below:

```
Thanks for testing Clickatell's gateway coverage. You will be able to change the content of your message after your initial purchase of message credits.
```

### After upgrading your ClickAatell account to a paid account

Once you have upgraded your Clickatell account, you will need to change a
few parameters to get things working again.
In CiviCRM,  go to: **Administer** > **System Settings**>
**SMS Providers** and click Edit on your "Clickatell" provider. In the API
Parameters box, under the api_id line, add a from= and a mo=1 parameter.  The
from= number is the phone number associated with the api_id in your Clickatell
account.

Your API parameters should now look like:
```
api_id=8473658
from=15551234567
mo=1
```

Back in your ClickATell Developer's Central page, click on the America's 2 Way SMS
tab, then click Manage next to the phone number you are working with.  For the
Primary Callback:  Reply Path, choose "HTTP Get" from the dropdown.  In the target
address, enter the same address as used before:
Drupal: http://www.example.com/civicrm/sms/callback?provider=org.civicrm.sms.clickatell
Wordpress: https://www.example.org/?page=CiviCRM&q=civicrm%2Fsms%2Fcallback&provider=org.civicrm.sms.clickatell

## Configuring a Twilio SMS Gateway

### Registering for a Twilio account

Twilio offer a wide range of communications and telephony services in
most countries. CiviSMS uses the "Programmable SMS" feature. You can
register a free trial account, which will allow you to test the service.

To sign up, visit:

[https://www.twilio.com/try-twilio](https://www.twilio.com/try-twilio)

Once you have registered for an account, you will be asked to verify
your phone number via SMS or call. You will then be taken to the Console.

### Getting a phone number

Your trial account entitles you to a free rented Twilio phone number,
which you can use to send SMS. However, free accounts must verify
a phone number before it can receive SMS. For the purposes of this
guide, we will send a message to the same phone number you verified
in the registration step.

1. From the [Console homepage](https://www.twilio.com/console), click the
**Programmable SMS** product.

![Programmable SMS](../img/twilio-programmable-sms.png)

2. Click the red **Get Started** button.

3. Click the **Get a number** button, near the top of the page. A phone
number will be suggested to you. If the country of the number is
not accurate, use "Search for a different number". Otherwise, click
**Choose this number**, and **Done**, after noting down the number.

You now have a rented phone number that can send and receive SMS messages.

### Setting up your new phone number in Twilio

1. Click the **All Products & Services** icon, in the far left navigation bar.

2. Choose "Phone Numbers".

3. Click your new number from the list to set it up.

4. Under the "Messaging" section, change the "A message comes in webhook" to the
following, replacing example.com with your CiviCRM installation.

`https://example.com/civicrm/sms/callback?provider=org.civicrm.sms.twilio`

This will send any replies that your messages receive back to CiviCRM.

![Messaging](../img/twilio-number-callback.png)

5. Click "Save".

### Getting your Twilio provider settings

1. In the Twilio Console, go back to the [Console homepage](https://www.twilio.com/console).

2. Copy down your "Account SID" and "Auth Token" (you will need to click the token to reveal it).

You now have all of the information needed to configure SMS in CiviCRM.

### Setting up your new SMS Provider in CiviCRM

Now that you have a Twilio account with a phone number, it needs to be set up in CiviCRM.

1. Go to CiviCRM and go to: **Administer** > **System
Settings**> **SMS Providers**. Click **Add New Provider**.

![image](../img/CiviCRM_SMS_adding-a-provider-twilio.png)

2. Set up the provider as follows:

* Name: select "Twilio"
* Title: give the SMS provider a title user's will see (e.g. "Twilio SMS")
* Username: enter your "Account SID" from the previous step
* Password: enter your "Auth Token" from the previous step
* API type: leave as "http"
* API URL: leave as "https://api.twilio.com/"
* API Parameters: enter "From=" followed by your Twilio phone number from the
previous step, in international format with no spaces.

Click Save to create your provider.

### Testing CiviSMS

You can now use CiviSMS to send an SMS message to your phone number
(the one you verified in earlier steps).

See the Everyday Tasks section for some ways you can send messages.

If you reply to an SMS message, it will be created as an activity on your CiviCRM record.

### Upgrading Twilio

To properly use Twilio as an SMS gateway, you will need a paid account.

You can upgrade your account to a full account by clicking "Upgrade" in
the top right corner from the Twilio console.

Once you have upgraded, your rented phone number will be available for full use,
and charged against your Twilio credit. 

You will now be able to send SMS to any phone number.
# Setting up a SMS Provider for CiviSMSClickatellTwilio

This document serves to provide a description on how to Configure/Add a SMS Provider for the CiviSMS feature of CiviCRM.

!!! danger "Mass SMS is broken on CiviCRM 4.6."

    Please see [CRM-17331](https://issues.civicrm.org/jira/browse/CRM-17331) for details. To fix this problem, either upgrade to CiviCRM 4.7 or backport the patch listed on CRM-17331.


# Configure Clickatell or Twilio

## **Configure Clickatell**

### **Create a Clickatell account**

First of all it is necessary to have an account of the SMS Provider that we are going to use, Clickatell, in our case.

Go to the Clickatell website ([http://www.clickatell.com/](http://www.clickatell.com/)) and sign up for an SMS Gateway, Clickatell Central account. Signing up to Clickatell Central is free, as a bonus you get 10 free message credits.

Note: the Developer's Central API is "global" which does not include the US. So, if you want to send to recipients in the US, you need the Americas 2 Way SMS API (formerly known as "Small Business API") which is a 2-way number. (Sending SMS with the gateway account seems to work on CiviCRM's side, but results in a "routing error" on the Clickatell side). You may have to pay the monthly fee for at least one month to get everything set up on this kind of account.

#### **Create a Clickatell HTTP API product**

In Clickatell Central, use the Manage My Products page to add a HTTP connection. This will generate an **API ID** that, together with your username and password, you need to add it to the SMS Provider settings of CiviCRM.

#### **Enable SMS Status Notification (MT Callback)**

Final or intermediary statuses are passed back by the API depending on the callback value set in the original post / sms

The sms code by default sets the callback to the value 3 which Returns both intermediate and final statuses of a message if callback url is set.

A callback url could be setup in your Clickatell central account to receive 'SMS Status notifications'.

The example of a url is:
 Drupal: [http://www.example.com/civicrm/sms/callback?provider=org.civicrm.sms.clickatell](http://www.example.com/civicrm/sms/callback?provider=org.civicrm.sms.clickatell)
 Wordpress: [https://www.example.org/?page=CiviCRM&q=civicrm%2Fsms%2Fcallback&provider=org.civicrm.sms.clickatell](https://www.example.org/?page=CiviCRM&q=civicrm%2Fsms%2Fcallback&provider=org.civicrm.sms.clickatell)

[Use HTTP GET / POST options for callback URL.](https://fisheye2.atlassian.com/changelog/CiviCRM?cs=42566)

#### **Handling replies**

This is only valid if you have signed up and paid for two-way messaging service.

1. In the Clickatell menu, click on "Americas 2 Way SMS" or "Small Business API" in the top navigation menu.
1. On the following screen, you should see your assigned 2-way number. Click the "manage" link next to it.
1. Under "Primary Callback", set a callback method (Reply Path), either HTTP POST (preferred) or HTTP GET (also works).
1. For the "Target Address", supply the same callback URL you used above. You don't need a username or password.
1. Click "Save Changes".

All Inbound SMS are created as activities in CiviCRM against the TO and FROM contacts.

#### **Short codes**

**todo** : The code currently doesn't support or does anything on its own for short codes. Not sure about renting process.

### **Download & Install extension**

Download and install required extension from the extensions directory. **Administer menu > System Settings > Manage Extensions** , then "Add New" and find the Clickatell extension.

NOTE: Clickatell requires a country code on your mobile phone numbers. For instance, US-based numbers can't be "212-555-1212", but "1-212-555-1212" works. There is a patched version of the Clickatell extension that removes this requirement, but assumes you are only contacting have US and/or UK numbers here: [https://github.com/PalanteJon/org.civicrm.sms.clickatell](https://github.com/PalanteJon/org.civicrm.sms.clickatell)

### **Configure SMS Provider Settings in CiviCRM**

To reach the SMS provider settings page, navigate through the menus as follows:

1. **Administer » System Settings » SMS Providers**
1. Click on " **Add New Provider**" button.
1. In the Manage SMS Provider Page, the provider **Clickatell** is selected by default(as Clickatell is the only sms provider configured for CiviCRM presently).
1. Type some name for your sms provider in the **Title** textbox.
1. Type in the **Username** and **Password** of your Clickatell account. Be aware that your Clickatell API password may differ from your Clickatell account password.
1. Select " **http**" for the **API Type**. The **API Url** defaults to "[https://api.clickatell.com](https://api.clickatell.com/)" for Clickatell.
1. Under **API Parameters** , you can provide the parameters separated by new line that is to be passed in the post data.

Like: api_id=1234567 **(For API ID, Refer step #2 )**

1. If you have a two way account (including Small Business API/America 2 Way SMS API) you need two additional **API Parameters:**

from=[11 digit number that clicktell provides]

mo=1

So an example configuration on the CiviCRM side under SMS Provider API parameters might be (with 2 way messaging):

```
api_id=1234567
from=15558238168
mo=1
```

**Now, the SMS Provider is configured and it can be used for sending SMS's to contacts.**

### **Test account**

**For test account, there is no need to create a Clickatell account.**

Refer the following steps to create a Test Account:

1. Follow the same steps as above i.e. **steps 3(a), 3(b), 3(c) and 3(d).**
1. You can provide any values for the **Username** and **Password**. eg username = "testuser", password = "testpassword".
1. Select the **API Type** as " **http**" and leave the **API Url** as it is(It defaults to "[https://api.clickatell.com](https://api.clickatell.com/)" for Clickatell).
1. Under **API Parameters** , provide the parameters as **is_test = 1** and **api_id = 1234567** or some random number, the parameters being separated by a new line.

**Now, the test account of the SMS Provider is configured and can be used for sending SMS.**

## **Configure Twilio**

### **Create a Twilio Account**

Go to the Twilio website ([http://www.twilio.com](http://www.twilio.com)) and sign up for an account. You can create a trial account and test, but you will need to upgrade your account in order to send SMS messages properly.

### **Download & Install extension**

In the CiviCRM Administer Menu, click on System Settings and then Manage Extensions. Click on the Add New tab and install the twilio extension.

Alternatively, you can download and install required extension from github - [https://github.com/civicrm/civicrm-core/tree/master/tools/extensions](https://github.com/civicrm/civicrm-core/tree/master/tools/extensions).

For setting up twilio the extension is : org.civicrm.sms.twilio

### **Configure SMS Provider Settings in CiviCRM**

To reach the SMS provider settings page, navigate through the menus as follows:

1. **Administer » System Settings » SMS Providers**
1. Click on " **Add New Provider**" button.
1. In the Manage SMS Provider Page, the providers that are enabled are available. Choose Twilio.
1. Type some name for your sms provider in the **Title** textbox.
1. The **Username** is the AccountSid.
1. The **Password** is your AUTH token.
1. Select **"http**" for the **API Type**. The **API Url** is [https://api.twilio.com/](https://api.twilio.com/) for Twilio/
1. Under **API Parameters** , you need to put in the phone number Twilio provides for your account, like this:
 From=+12345678901 (enter +1 in front of your number, no spaces, dashes or brackets in the number). Swap 1 with the country-code of your Twilio phone number, ie 1 is the country code for USA and Canada.

### **Configure for inbound SMS**

If you want the incoming SMS to be recorded in CiviCRM, follow the steps:

1. In Twilio account settings, go to Numbers and click on the number you want CiviCRM to "listen to". That will open configuration settings for that phone number. Under Messaging, change **Request URL** to [http://yoursiteurl/civicrm/sms/callback?provider=org.civicrm.sms.twilio](http://yoursiteurl/civicrm/sms/callback?provider=org.civicrm.sms.twilio) with **HTTP POST**
1. In CiviCRM, under SMS Providers, edit your Twilio settings and add to API Parameters: mo=1 so that in API Parameters there is:
 From=+14165678901
 mo=1# Adding Sender ID(s) to your SMS messages (clickatell only)

## Register Sender ID

First you need to register your new Sender ID(s) on the clickatell site:

Log into your account at [http://www.clickatell.com/](http://www.clickatell.com/)

1. Click on the My Settings tab.
1. Click on Manage Sender IDs link.
1. Click on Add Sender ID.

After completing these steps, the online interface will guide you through the process of
 applying for a custom Sender ID. You will receive email notifications if any further action is
 required for the process to continue.

[Full documentation of Clickatell Sender ID](http://www.clickatell.com/downloads/Clickatell_Sender_ID_User_Guide.pdf)

## Add to CiviSMS

Once approved you can add the sender ID to your default account simply by adding the line:

from=MySenderID

(where 'MySenderID' is the sender ID listed at your Clickatell [SenderID management page](https://central.clickatell.com/central/central/dashboard/senderid_dashboard.php)) to the **API Parameters** of the **SMS Provider Settings** in CiviCRM.

To add multiple Sender IDs you need to add extra **SMS providers** using the same details but substituting a different SenderID in the from parameter

See Also: [Setting up a SMS Provider for CiviSMS](http://wiki.civicrm.org/confluence/display/CRMDOC42/Setting+up+a+SMS+Provider+for+CiviSMS)