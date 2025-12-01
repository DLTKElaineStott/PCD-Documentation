<style>

.zDocsTocHeader {

 background-color:rgb(255, 255, 255) !important;

 padding: 0px 0px; /* Add space for visual height if you want */

}

[data-testid="toc-container"] .zDocsTocResizable {

 width: 10px !important;

 min-width: 1px !important;

 max-width: 1px !important;

 overflow: hidden !important;

}

.sticky-pane-image {

 position: sticky !important;

 top: 40px !important; /* Adjust as needed for your header */

 align-self: flex-start !important;

 z-index: 2 !important;

}

.sticky-pane-image img {

 pointer-events: none !important;

}

.parent-class-with-overflow, .publish-pane-section {

 overflow: visible !important;

}

/* Optional: for future layout control */

.publish-pane-section {

 display: flex;

 flex-wrap: wrap;

 gap: 20px;

 align-items: flex-start;

 /* DO NOT add overflow: hidden/auto/scroll here! */

}

</style>

<link rel="stylesheet" href="https://education.deltek.com/web/dlh/deltek-styles.css">

# Github Testing File
# Overview

Use Vantagepoint Payments: AP Automation (an integration between Vantagepoint and Corpay) to simplify and automate U.S. dollar payments to your vendors. When you process vendor payments in Vantagepoint, you securely send Corpay a text (.txt) file that contains information from the vendor payment run. Corpay then uses the information in this file to pay your vendors.

At Corpay's AP Gateway site, you can view actual payment detail and history for your vendors. You can also see payment clearance information, approved payments, vendor payment methods, and vendor payment reports.

This guide provides you with information on how to:

- Set up the integration between Vantagepoint and Corpay

- Process vendor payments in Vantagepoint and send them to Corpay to pay

- Reconcile Corpay payments to Vantagepoint

- Complete bank reconciliations in Vantagepoint for Corpay-related banks

- Add new vendors and change vendor addresses

- Contact Corpay Technical Support

# Vantagepoint Payments: AP Automation Setup Between Vantagepoint and Corpay

To set up Vantagepoint Payments: AP Automation, you complete setup steps in Vantagepoint. You also work with a Corpay implementation specialist to complete the Corpay onboarding process. This section of the guide provides instructions for these integration setup steps.

If you have multiple companies in Vantagepoint, you complete these steps for each company that will use Corpay to pay vendors.

## Vantagepoint Prerequisites

Before you can set up and use Vantagepoint Payments: AP Automation in Vantagepoint, you must complete the prerequisites below.

### Vantagepoint Accounting Module

You must have a license for the Vantagepoint Accounting module.

### User Login Security Role

Your user login security role in Vantagepoint must have menu access to **Utilities » Integrations » Corpay**.

To perform the integration setup, you must add a special Corpay Payment bank and Corpay Suspense general ledger account. If your user login security role does not give you access to **Settings » Cash Management » Banks** and **Settings » General Ledger » Chart of Accounts**, a user with this security access needs to perform these steps.

## Vantagepoint Setup Required Before Completing the Corpay Onboarding Process

### Extract and Send Vendor Information to Corpay

Before you sign up to use Corpay's vendor payment services, they will request information for all your vendors that they would be paying. This helps them better assess your situation. Use the Corpay Integration utility in Vantagepoint to automatically extract vendor information from the Vantagepoint Firms hub, add the information to a comma-separated values (.csv) file, and download the file to send to Corpay.

Corpay may ask you to complete the vendor extract process again when you first start to use Corpay. Otherwise, you don't need to perform this task again. For instructions on how to process changes to vendor address information and add new vendors after you send the .csv file to Corpay, see the _New Vendors and Vendor Address Changes After the Initial Integration Setup_ section in this guide.

### Contents of the .csv File that You Send to Corpay

The .csv file that you automatically generate using the Vantagepoint Corpay Integration utility contains the following from the Vantagepoint Firms hub:

- All active vendors approved for processing that have a payment address defined in the Firms hub.

- Information that Corpay requires to make payments to a vendor, such as vendor name, address, federal ID, and so on.

#### Prerequisites

Before you extract and send vendor information to Corpay, be sure that the following settings are entered for each vendor in the Vantagepoint Firms hub that Corpay will be paying:

- In the Summary pane, set the **Status** field to **Active**.

- On the Vendor tab, select the **Approved for use in processing** check box.

- In the Addresses grid on the Overview tab, select the **Payment** check box for the address in the grid that is the address to send vendor payments to.

  The payment address must have values in the following fields: **Address 1**, **City**, **State/Province**, and **Zip/Postal Code**. You may want to perform a search in the Firms hub to find vendors that have missing entries in any of these fields.

<div class="note note note_note"><strong>Important:</strong> The payment address <strong>Description</strong> field in the Firms hub is an important part of the key that Corpay uses to keep vendor information unique in their system. You should not make changes to this field after you send Corpay the .csv file and they set up the vendor in Corpay. You may want to review the <strong>Description</strong> field for your vendors and make any changes before you send vendor information to Corpay.</div>

#### Extract and Send Vendor Information to Corpay

<div class="word-special word-procedure Procedure">To extract vendor information from the Firms hub and send it to Corpay:</div>

1.  On the Corpay form in **Utilities » Integrations » Corpay**, click **Actions » Vendors Extract**.

2.  On the Vendors Extract Action dialog box, click **Yes** to create and download the .csv file.

      The .csv file is automatically downloaded and saved to your local computer.

<div class="note note note_note"><strong>Important:</strong> You can view the .csv file, but <strong>do not</strong> resave it. Resaving could reformat some fields.</div>

  3.  When Corpay requests this information, send the original, downloaded .csv file to them.

### Set Up a Corpay Suspense Account

Set up a new asset account in **Settings » General Ledger » Chart of Accounts**.

Deltek recommends that you name this account "Corpay Suspense."

If you use multiple currencies in Vantagepoint, you must set this account's **Currency Code** field to **USD** (U.S. dollars).

The Corpay Suspense account is:

- Credited when you process a vendor payment run in Vantagepoint for Corpay to pay.

- Debited when you enter a cash disbursement in Vantagepoint for your regular bank account to record the withdrawal that Corpay makes from your bank for the vendor payments.

### Set Up a Bank for Processing Corpay Vendor Payments

Set up a new bank in Vantagepoint to use for processing Corpay payments.

This bank is **not** a real bank account with your actual bank. It is **not** the bank that Corpay charges to receive payment from you for vendor payment runs. This new bank is used only for integration purposes when you process a vendor payment run in Vantagepoint. For this bank, you define the contents and formatting for the payment file (a .txt file) that Corpay requires each time you process a vendor payment run for them to pay.

Deltek recommends that you name the new bank "Corpay Payment," so the vendor payments that are paid by Corpay are stored with this reference.

When you set up the Corpay Payment bank in **Settings » Cash Management » Banks**, enter the following for the bank on the Options tab:

- In the **Account** field enter the Corpay Suspense account that you created per the previous section of this guide.

- If you use multiple currencies in Vantagepoint, set the **Currency** field to **USD** (U.S. dollars).

- Set the **Export to Text Format** field to **User Defined**.

<div class="note note note_note"><strong>Your Regular Bank Account:</strong> The bank that Corpay charges to receive payment from you for vendor payment runs is your regular bank account. Your regular bank should be an existing bank, already set up in Vantagepoint. Corpay withdraws the total amount of a vendor payment run from your regular bank account. As a result, the bank reconciliation for your regular bank account in Vantagepoint is simple. There will be only one payment amount on your bank statement for the entire payment run, regardless of how many vendors were paid in the payment run.</div>

### Define the Contents of the Payment File that Corpay Requires for Each Vendor Payment Run

For the Corpay Payment bank, you define the contents and formatting for the payment file (a .txt file) that Corpay requires each time you process a vendor payment run for them to pay.

Complete the steps in this section to have Vantagepoint automatically define the contents and formatting for the .txt file. The Vantagepoint fields included in the .txt file and the formatting for the file are automatically updated on the User Defined File Format tab in **Settings » Cash Management » Banks** for the Corpay Payment bank.

<div class="word-special word-procedure Procedure">To automatically define the contents of the .txt file that Corpay requires for vendor payment processing:</div>

1.  In the Navigation pane in Vantagepoint, select **Utilities » Integrations » Corpay.**

2.  On the Corpay form, click **Actions » Update Bank**.

      The **Update Bank** action displays if your security role has access to **Settings » Cash Management » Banks.**

3.  On the Update Bank Action dialog box, click **Yes** to proceed with the update.

4.  On the Select Bank dialog box, select the Corpay Payment bank that you set up for Corpay payment processing.

      This is the bank that is not a real bank. It is used only for processing Corpay vendor payments.

<div class="note note note_note"><strong>Important:</strong> Do <strong>not</strong> select your regular bank account that Corpay will use to withdraw money from your bank to pay for the vendor payment run.</div>

5.  Click **Update**.

      The updating occurs on the User Defined File Format tab in **Settings » Cash Management » Banks** for the Corpay Payment bank. It occurs without opening **Settings » Cash Management » Banks**.

      Existing data in all of the grids on the User Defined File Format tab is removed.

      The Vantagepoint fields and formatting for the .txt file are added to the Vendor grid on the tab.

      After the updating completes, you receive a message to let you know it was successful.

6.  To see the rows added to the Vendor grid on the User Defined File Format tab:

    a.      In the Navigation pane, select **Settings » Cash Management » Banks**.

    b.      On the Banks form, select the Corpay Payment bank.

    c.      Click the User Defined File Format tab.

    d.      In the **Line Type** field, select **Detail 1**.
    
    e.      Review the contents in the Vendor grid---you should see 25 fields.
            
      These are the fields that will be included in the .txt file that is automatically sent to Corpay when you process a vendor payment run for them to pay.

### Set Up Auto Numbering for the Corpay Payment Bank

Deltek recommends that you use auto numbering with a prefix of CP for the vendor payments that you process using the Corpay Payment bank. When you do this, a Vantagepoint reference number is automatically assigned to each payment in the vendor payment run. This allows you to easily identify payments made by Corpay.

**To set up auto numbering for the Corpay Payment bank:**

1.  In the Navigation pane, select **Settings » Accounting » Transactions**.

2.  On the Transaction Settings form, go to the Transaction Auto Numbering Options section.

3.  In the Bank Transactions grid, click **+ Add Bank** to insert a new row in the grid.

4.  In the **Bank Code** field, select **Corpay Payment**.

5.  Leave the check box in the **Receipts** field cleared.

6.  Select the check box in the **Payments** field.

7.  In the **Payments to Autonumber** field, select **All Payments**.

8.  In the **Autonumber Prefix** field, enter **CP**.

9.  In the **Next Payment Reference Number** field, enter **1**.

10. Review the **Next Payment Reference Number** example field to see the **CP** prefix combined with the number **1**.

11. Click **Save**.

## Corpay Onboarding Process

You work with a Corpay implementation specialist to complete the Corpay onboarding process.

### Location ID

During the onboarding process, if you have multiple companies in Vantagepoint, Corpay will ask you for a location ID for each company that uses Corpay to make vendor payments. The location ID is the company code entered for a company in the **Codes** field on the Subcodes tab in **Settings » Organization » Codes** in the desktop application.

If you have only one company in Vantagepoint (a single-company database), no company code is entered in Vantagepoint for it. Corpay uses a null value for the location ID.

### Your Bank That Corpay Will Withdraw Funds From

Corpay helps you access and set up your Corpay AP Gateway. You provide Corpay with information for the bank that you are allowing Corpay to withdraw funds from after they pay vendors in a vendor payment run. If you have multiple companies in Vantagepoint, you provide Corpay with bank information for each company (location ID) that uses Corpay to make vendor payments.

Corpay provides you with their ACH Originator ID and information on how to pre-authorize Corpay on your bank account so the withdrawals that they make go through without requiring another set of approvals.

### Corpay Identifiers to Enter in Vantagepoint

During the onboarding process, Corpay provides you with the following unique identifiers to enter in the Vantagepoint Corpay Integration utility (**Utilities » Integrations » Corpay**) before you can complete a vendor payment run in Vantagepoint that Corpay pays:

- Corpay Organization ID

- Corpay Company ID

- Corpay Client ID

- Corpay Client Secret

If you have multiple companies in Vantagepoint, you will have identifiers for each company (location ID) that uses Corpay to make vendor payments.

### Corpay Approval System

Corpay has an optional approval system that you can use in addition to, or instead of, the AP approval system in Vantagepoint. Corpay can provide more information about their approval system and help you implement it.

If you use the Corpay approval system and an AP payment is not approved in Corpay, the payment will be excluded from the Corpay payment run. You need to void this payment that has already been posted in Vantagepoint.

## Enter Corpay Identifiers in Vantagepoint

During the Corpay onboarding process, Corpay provides you with identifiers that you must enter in the Corpay integration utility in Vantagepoint before you can process Corpay vendor payments in Vantagepoint. If you have multiple companies, you do this for each company that uses Corpay to make vendor payments.

<div class="word-special word-procedure Procedure">To enter the Corpay identifiers in Vantagepoint:</div>

1.  In the Navigation pane, select **Utilities » Integrations » Corpay**.

2.  On the Corpay form, enter information that Corpay provided in each of the following fields:

- **Corpay Organization ID**

- **Corpay Company ID**

- **Corpay Client ID**

- **Corpay Client Secret**

After you make an entry in a field, the field contents are blurred so that the codes are not visible to anyone looking at your screen. To display the code, place your mouse cursor in a field again.

<div class="note note note_note"><strong>Important:</strong> Entries in all the fields on the Corpay form are required for the integration between Vantagepoint and Corpay to work properly.</div>

# Process Vendor Payments in Vantagepoint to Send to Corpay

The steps below provide a summary of how to process vendor payments in Vantagepoint for Corpay to pay.

<div class="note note note_note"><strong>Important:</strong> Make sure your vendor payment run is using the bank that you created in Vantagepoint for processing Corpay vendor payments. You assign the bank to a voucher when you enter the voucher in <strong>Transaction Center » Transaction Entry » AP Vouchers</strong>. If you did not assign this bank when you originally entered and posted the voucher, you can edit the bank for a voucher during the Select Vouchers step of a vendor payment run. During this step, click the amount in the <strong>Payment Amount</strong> field in the Vendor Balances grid. In the Select Vouchers to Pay dialog box, edit the bank.</div>

1.  In **Cash Management » Vendor Payments**, create a new vendor payment run.

      On the New Payment Run page, complete the entries in the fields and be sure to:

      a.    Set **Payment Type** to **Check/EFT**.

      b.    Set **Process Type** to **Automatic** or **Manual**.

2.  On the Select Vouchers page:

      a.      Select the vouchers to be paid for a manual run and review the vouchers to be paid in an automatic run.

      b.      Click **Process Payments** to see the payments from each bank.

3.  On the Printing and Numbering page, perform the following for the Corpay Payment bank:

      a.      Click the **Print Payments** action to move to the next step of the payment process.

      b.      When the payment preview is generated, close the dialog box without printing the payments.

      c.      Click the **Export to Text** action.

      d.      On the Download Payments to Text File dialog box, click **Corpay** to create and electronically send the .txt file with vendor payment information directly to Corpay.

      e.      Click the **Assign Numbers** actions to assign payment numbers.

      Numbers are assigned based on the auto numbering you set up for the Corpay Payment bank (per the _Set Up Auto Numbering for the Bank_ section above).
      
      f.      You do not need to print a file copy or remittances.
      
      g.      Click the **Post Payments** action to post the vendor payment run.

4.  Use the Corpay AP Gateway site to see that all the payments in a payment run were accepted and that the total amount is what you expect.

# Vendor Payments Excluded by Corpay

It is possible that Corpay may not be able to process an individual payment to a vendor in a vendor payment run or the entire payment run. At Corpay's AP Gateway, you see these excluded individual vendor payments and excluded entire payment runs listed, along with the reason for the exclusion.

Common reasons for being excluded:

- Information for a vendor was missing from the vendor payment file.

- A vendor payment run was a duplicate of a previous vendor payment run that Corpay already paid. Corpay detects a duplicate payment run if a payment file has the same invoice numbers and payment amounts for vendors that match those in a previous payment file.

## Individual Payment in a Vendor Payment Run Is Excluded

If an individual payment in a vendor payment run is excluded by Corpay:

1.  In Vantagepoint **Cash Management » Payment Review**, void the payment. Use the same process that you use to void a check or EFT payment. Select the payment in the Payments grid and click **Void**.

2.  Make the needed corrections for the vendor.

3.  Include the vendor in another vendor payment run to send to Corpay.

## Entire Vendor Payment Run Is Excluded

If an entire vendor payment run is excluded by Corpay:

1.  For payment runs that are **not** posted in Vantagepoint, delete the payment run in **Cash Management » Vendor Payments**. On the Printing and Numbering page of the Vendor Payments form, click **Other Actions »** **Delete Run**.

2.  For payment runs that are already posted in Vantagepoint, unpost the payment run in Vantagepoint in **Utilities » Transactions » Undo Postings**.

3.  Correct the problem that caused the payment run to be excluded by Corpay.

4.  Create a new vendor payment run in Vantagepoint and send it to Corpay.

## Recurring Payments

You may have recurring payments for a vendor that intentionally have the same AP invoice number and the same payment amount. When Corpay detects this, it is consider to be a duplicate payment. Corpay will contact you to confirm whether or not this is a legitimate payment for them to make. If they don't hear back from you in a certain amount of time, they will exclude the payment from their payment batch. If they exclude it and it is a legitimate payment to make, you can include the payment in another payment run for Corpay.

For recurring payments, you can also add a comment to an AP invoice payment in Vantagepoint to indicate that the payment is legitimate. Corpay reviews these comments and will not exclude the payment if they see a note for it.

## Partial Payments

When you make partial payments for an AP invoice, you intentionally use the same AP invoice number and usually a different payment amount for a subsequent payment. With the different payment amounts, Corpay interprets the subsequent payment with the same invoice number as a legitimate payment and does not exclude it from the payment batch.

# Pay Corpay for Vendor Payment Runs

Corpay processes and sends payments to your vendors before they charge your regular bank account (an ACH withdrawal) for the total amount of the vendor payment run. Corpay doesn't wait until they are paid by you before they pay your vendors.

The charge to your bank usually occurs overnight after the day that the payment run is accepted. You see the charge to your bank account within approximately two business days.

If you send Corpay a vendor payment file close to 5 PM Pacific time, which is their cutoff time for processing transactions, it is possible that they will process only part of the vendor payment run that day and finish processing the remainder on the next day. In this scenario, you could see a withdrawal from your bank account for the total of the vendor payments processed one day and another withdrawal for the total of the vendor payments processed the next day.

In Vantagepoint, you enter a manual cash disbursement transaction to record the ACH withdrawal from your bank account made by Corpay for a vendor payment run. You may need to enter one or multiple cash disbursements for a vendor payment run, based on whether or not Corpay split the payment processing over multiple days.

<div class="word-special word-procedure Procedure">To process the cash disbursement:</div>

1.  In the Vantagepoint Navigation pane, select **Transaction Center » Transaction Entry » Cash Disbursements**.

2.  On the Cash Disbursements form, in the **Bank** field, enter your regular bank account that Corpay charged for the vendor payment run. This bank should already be set up in Vantagepoint.

3.  In the Project Information grid, add a detail line for payment:

      a.   In the **Amount** field, enter the total amount of the vendor payment run that was paid to Corpay.
      
      b.  In the **Account** field, enter the Corpay Suspense asset account that you set up for the Corpay Payment bank for processing the vendor payment run in Vantagepoint.

The Cash Disbursement entry:

- Debits the Corpay Suspense account

- Credits the account for your regular bank account

The debit to the Corpay Suspense account offsets the credit made to the Corpay Suspense account when you used the Corpay Payment bank for processing the vendor payment run in Vantagepoint.

# Reconcile Corpay Payments to Vantagepoint

After each accepted Corpay vendor payment run, complete the actions in this section.

## Verify that the Amount Corpay Paid Equals the Amount on the Vantagepoint Posting Log

When Corpay indicates that a payment run is accepted, check that the total amount Corpay paid to all the vendors in the payment run matches the total amount paid per the Vantagepoint Vendor Payments posting log.

  Use the Corpay AP Gateway to check the total amount that Corpay paid for a vendor payment run.

Check the Vantagepoint Vendor Payments Posting log in **Transaction Center » Posting Review**.

### Payment Amounts in Vantagepoint and Corpay That Don't Match

Corpay's cut-off time for processing transactions is 5 PM Pacific time. If you send Corpay a vendor payment file close to 5 PM, it is possible that the vendor payments will be partially processed that day and the remainder will be processed the next day. With the processing split over two days, you will see separate total payment amounts for each day in the Corpay AP Gateway that make up the one total amount in Vantagepoint.

If you use Corpay's approval system, and some invoices in a vendor payment file are not approved for payment, the total amount of the invoices paid by Corpay won't match the vendor payment run total in Vantagepoint. For this scenario, you need to void the invoices in Vantagepoint that were not approved and not paid by Corpay.

## Post a Cash Disbursement to Your Regular Bank Account for the Corpay Withdrawal

Corpay charges the total amount of an accepted vendor payment run to your regular bank account. To record this payment in Vantagepoint, enter and post a manual cash disbursement from your regular bank account for the total vendor payment amount.

For more information, see the _Pay Corpay for Vendor Payment Runs_ section above.

## Ensure that the Amount in the Corpay Suspense Account is Zero

In Vantagepoint, use the Account Analysis report in **My Stuff » Reporting** to verify that the Corpay Suspense account has a closing balance of \$0.00. For easy review, display only the Corpay Suspense account on the report.

The Corpay Suspense account is:

- Credited when you process a vendor payment run for Corpay in Vendor Payments in Vantagepoint (using the Corpay Payment bank)

- Debited when you enter the cash disbursement to record Corpay\'s withdrawal from your regular bank account for the total amount of a vendor payment run they paid

- Debited when you void an excluded payment

# Bank Reconciliation in Vantagepoint

Bank reconciliation in **Cash Management » Bank Reconciliation** is easy when vendor payments are paid by Corpay. You no longer need to match each individual vendor payment to a separate item on your bank statement.

## Your Regular Bank Account for Paying Corpay

Your regular bank account is charged with a withdrawal transaction when Corpay pays a vendor payment run. You enter a cash disbursement transaction for your regular bank to record this payment made to Corpay. However, in certain situations, you may have more than a single amount to match.

Corpay's cut-off time for processing transactions is 5 PM Pacific time. If you send Corpay a vendor payment file close to 5 PM, it is possible that the vendor payments will be partially processed that day and the remainder will be processed the next day. With the processing split over two days, you see separate withdrawal amounts for each day in the Corpay AP Gateway and on your bank statement. You enter two cash disbursements for the two separate withdrawals.

Corpay provides several reports to help you with your Bank Reconciliation. See Corpay's AP Gateway User Guide for a list of all their reports.

### If You Import Bank Transactions for Your Bank Reconciliation

The cash disbursement transaction matches automatically to the bank's payment to Corpay on the bank statement and is automatically cleared on the statement.

### If You Do Not Import Bank Transactions for Your Bank Reconciliation

Use the Payments tab in Bank Reconciliation to find payments that are listed as Cleared on the bank statement. If you see the Corpay withdrawal on the bank statement, select the cash disbursement transaction that you created and select the **Cleared** check box.

## Corpay Payment Bank

No bank reconciliation is needed for the Corpay Payment bank that you use when you process vendor payments in Vantagepoint for Corpay to pay.

# New Vendors and Vendor Address Changes After the Initial Integration Setup

After you complete the initial integration setup between Corpay and Vantagepoint and Corpay has been paying your vendors for a period of time, you may have new vendors for Corpay to pay or address changes for existing vendors. For these scenarios, all you need to do is enter a new vendor or change an existing vendor's address in the Firms hub in Vantagepoint.

## New Vendors

### What to Do in Vantagepoint

Enter new vendors in the Firms hub.

In the Address grid on the Overview tab of the Firms hub, be sure to enter the following information for the vendor's address, so Corpay can successfully process payments for the new vendor:

- Select the **Payment** check box to indicate that this is the address to which to send payments.

- Enter values in the **Address 1**, **City**, **State/Province**, and **Zip/Postal Code** fields.

### How Corpay Handles New Vendors

When you include a new vendor in a vendor payment run and then export the payment file to Corpay, Corpay does the following:

The new vendor's payment is identified as a first-time payment. Corpay automatically creates a new vendor record in their system and makes the first payment to the vendor using a printed check. Then Corpay contacts the vendor for electronic payment enrollment.

## Existing Vendors with an Address Change

### What to Do in Vantagepoint

If an existing vendor has a change to their payment address or a new payment address, update the existing address or add a new address in the Address grid on the Overview tab of the Firms hub.

Be sure that the **Payment** check box is selected for the address and that you enter values in the **Address 1**, **City**, **State/Province**, and **Zip/Postal Code** fields.

<div class="note note note_note"><strong>Important</strong>: The address <strong>Description</strong> field for the payment address in the Firms hub is an important part of the key that Corpay uses to keep vendor information unique in their system. Be sure about the description that you enter in this field because you should not change it after Corpay receives payment information for the vendor and they set up the vendor in their system.</div>

### How Corpay Handles the Address Change

When you include an existing vendor with an address change in a vendor payment run in Vantagepoint and then export the payment file to Corpay, Corpay sends the payment to the address in the vendor payment file. Corpay always sends payments to the address in the vendor payment file.

# Contacting Corpay Technical Support

For any issues that occur in Vantagepoint during the vendor payment process, contact Deltek Vantagepoint Customer Support.

For any issues that occur after Corpay receives a vendor payment .txt file from Vantagepoint, contact the Corpay Technical Support team for assistance:

- techsupport@nvoicepay.com

- 1.877.974.1752
