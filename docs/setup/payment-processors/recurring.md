# Recurring Contributions and Webhooks

## Overview
Most payment processors that support recurring contributions use a "webhook" which is used by the payment processor
to notify CiviCRM of each new payment, whether successful or failed.

If webhooks are not setup correctly you may see one or more of the following symptoms in CiviCRM:
* Payments not marked as "Completed".
* New payments not recorded in CiviCRM.

If you are not using recurring contributions the webhook is not normally required.

## IPN notify URL
!!! tip
    Follow the instructions for the specific payment processor - when you get to the part where you need to configure the IPN notify URL / Webhook read below to work out what that URL should be.
 

At this point, you need to figure out the "URL to be called" value. To do this, you need to check what ID is assigned to the Stripe payment processor in CiviCRM.

To determine the ID, go back to CiviCRM and click `Administer -> System Settings -> Payment Processor`

Click Edit next to the payment processor you are setting up.

Then, check the Address bar. You should see something like the following:

https://example.com/civicrm/admin/paymentProcessor?action=update&id=3&reset=1

The end of the address contains id=3. That means that this Payment Processor id is 3.

Therefore the call back address for your site will be:

    civicrm/payment/ipn/3

See below for the full address to add to the endpoint (replace NN with your actual ID number):

* For Drupal:  https://example.com/civicrm/payment/ipn/NN
* For Joomla:  https://example.com/?option=com_civicrm&task=civicrm/payment/ipn/NN
* For Wordpress:  https://example.com/?page=CiviCRM&q=civicrm/payment/ipn/NN

Typically, you only need to configure the end point to send live transactions and you want it to send all events.
