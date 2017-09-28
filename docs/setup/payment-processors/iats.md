# iATS Payments Configuration

1. Read the [extension's README](https://github.com/iATSPayments/com.iatspayments.civicrm/blob/master/README.md)
for notes about setting up your iATS account setting permissions, as well as for server requirements and other helpful configuration notes.

2. Sign up for an account with [iATS Payments](http://home.iatspayments.com/). If you're working with a CiviCRM developer, they may have an agency-specific page where you should sign up so that iATS knows who you are working with. When you signup, tell them you are using CiviCRM, it helps them set up your account appropriately. You also need to tell them if you want ACH/EFT and/or Swipe support for the account. And make sure you're setting up an account for the right region (i.e. the North America, or UK server).

3. Once your account is approved (which may take a few days to a few weeks, depending), you'll need the [iATS Services Payment Processor extension](https://civicrm.org/extensions/iats-payments), installed in the usual way.

4. You create a payment processor for each type of payment (i.e. credit card, ACH/EFT, swipe) you'll be receving. The credentials are usually the same for each one.
 
5. Questions? Use the [github issue queue](https://github.com/iATSPayments/com.iatspayments.civicrm/issues).
