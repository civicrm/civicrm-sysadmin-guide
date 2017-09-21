# iATS Payments Configuration

If you are looking for information about the old iATS payment processor, you can still find that [here](https://wiki.civicrm.org/confluence/display/CRMDOC44/IATS+Configuration).

But you really should now be using the new iATS Services Payment Processor packaged as an extension, linked below. Both processors can co-exist, and you can migrate to the new one from the old one.

[https://civicrm.org/extensions/iats-payments](https://civicrm.org/extensions/iats-payments)

Configuration Notes:

1. Be sure to tell iATS that you are using CiviCRM, they do know about it. You also need to tell them if you want ACH/EFT and/or swipe support.
1. See the readme for information about recommended practices re: accounts.
1. Make sure you're setting up an account for the right region (i.e. the North America, or UK server).
1. Questions? Use the github issue queue here: [https://github.com/iATSPayments/com.iatspayments.civicrm/issues](https://github.com/iATSPayments/com.iatspayments.civicrm/issues)