# Odoo 18

## Release Notes 

## Industries

Bakery

A new industry package is available for bakeries offering a variety of baked goods.

Cleaning Service

A new industry package is available for cleaning service companies.

Dropshipping

A new industry package is available for businesses that purchase from suppliers and ship directly from the supplier to the customer.

Electrician

A new industry package is available for electricians doing regular field service jobs.

Food Truck

A new industry package is available for food trucks and other mobile food businesses, such as ice cream trucks.

Law Firm

The Law Firm industry package has been updated to enhance case management and add legal requirements for lawyers, including KYC assessment and a three-level signature process.

Marketing Agency

A new industry package is available for marketing agencies.

Outdoor Activities

A new industry package is available for outdoor activities such as scuba diving, whitewater rafting, and other outdoor adventures.

Shoemaker

A new industry package is available for managing shoe repair and manufacturing.

Tattoo Shop

A new industry package is available for tattoo shops.

Wedding Planner

A new industry package is available to help wedding planners manage projects, customers, and vendors.

## Accounting 

Abnormal invoice alert

Added a statistics-based alert system to automatically identify abnormal amounts and dates in invoices.

Allow email aliases on MISC journals

Use email aliases on miscellaneous journals to automatically create journal entries from email attachments.

Analytic budgets

New budget management. No more dates on budget lines; no more budgetary positions.

Annual sequence and staggered fiscal years

The fiscal year sequence on journals can differ from calendar years to handle staggered fiscal years.

Ascending/descending report dates

Switch the order of your date columns (ascending/descending) when using date comparisons.

Audit trail improvements

The audit trail has been improved, namely in the context of the GoBD certification in Germany.

Autopost bills

Automatically post bills from chosen vendors.

Bank reconciliation: simplified batch payment matching and statement form view

Batch payments are represented by a single folded line in the Bank reconciliation widget to simplify the matching process. The statement form view can be opened from the Kanban view and includes a chatter with quick navigation to linked transactions.

Bank statement: import and OCR

Bank statements can be imported even when transactions are not sorted by date.
Upload PDFs or image files of bank statements to have the OCR extract the transactions automatically.

Bills payment wizard: QR codes

The vendor bills payment wizard integrates QR codes for outgoing payments.

Catalog view in Invoicing

The Catalog view is now available on customer invoices and vendor bills.

Charge bearer

Specify the charge bearer when generating batch payments for ISO20022 payment files. 

Check printing layout

Select check layouts in the bank journal settings.

Combine analytic distribution models

Sequences are now available on analytic distribution models, allowing distribution according to multiple models provided that they are distributed on different plans.

Configure layout wizard

The configure layout wizard shown when the user prints their first invoice was improved. The data of the actual invoice is used, the preview display is enhanced, and the QR code for SEPA payments is more easily added.

Credit card journal

A new credit card journal type was added to register credit card payments, upload statements, and handle reconciliation.

Currency exchange rate

The exchange rate is stored on invoices and displayed.

Customized invoice templates

Invoice templates can now be customized with Studio and declared as compatible with the Send & Print flow on invoices.

Date selector

The new date selector on reports enables users to navigate smoothly from one period to another.

Duplicate bill detection

Improved Vendor Bill duplicate detection. Potential duplicates (using Bill references) both in draft and posted entries are looked for in the database before creating the new move. Smart links are available to directly navigate to the potential duplicate(s).

Financial budgets

On the profit and loss report, display and compare the report's figures with financial budgets that are separate from analytic budgets.

Follow-up report, customer statement, and partner ledger revamp

The Follow-up and Customer Statement views and modules were removed and merged. Follow-up reports can be configured to automatically add followers to the client chatter on execution. Miscellaneous entries impacting the client account will be excluded from the reports by default. The Partner Ledger includes additional options and is displayed similarly to a customer statement, with unnecessary columns hidden. It can also be sent directly to customers.

Import matching numbers

Add a matching_number to your CSV import of lines. Odoo will wait for all related account moves to be posted and try to reproduce the reconciliation when that happens. If it fails, the imported reconciliation will be discarded.

Installment payments

Registering a payment from the invoice form view considers installment amounts defined on the payment terms. The customer portal clearly displays what has already been paid.

Intercompany transactions

Improve flexibility of managing intercompany transactions by using booleans instead of radio buttons.

Invoice Analysis report

The Invoice Analysis report offers margin and a simple inventory valuation measure based on customer invoices and vendor bills without requiring the Inventory app.

Journal email alias

If an email that contains no usable file is received on an email address set as a journal alias, an automatic response is sent to the sender informing them that no document was received.

Journal report

The UI of the journal report has been simplified. Performance is prioritized over detailed transactions, but detailed transactions remain available for export.

Legal notes on taxes

Specific legal notes can be added to taxes to be shown on documents when the tax is used. This enables covering more granular business cases than legal notes based on fiscal positions.

Loan management

Manage acquired loans with automated adjustments based on your defined or imported amortization schedule.

Lock dates wizard

Allow locking by journal type; add a hard lock date and exceptions management mechanism.

Matching numbers

Matching numbers have been simplified and are displayed with colors. Partial matches have been improved and specify which moves are partially matched together with a proper identifier.

Multi-ledger

Multi-ledger usability has been improved in multi-company settings for better grouping, default selection, and filtering. 

New documents layout

Three new document layouts were added for more customizations.

New product widget

Products and descriptions are now combined in a single column in invoice line edition.

OSS periodicity

OSS reports now follow their own periodic schedule, separate from the fiscal one. OSS sales reports are filed quarterly, while IOSS reports are filed monthly. 

Overdue invoices and online payments

Customers are shown overdue amounts on their portal. Smart links are provided both on the portal and in follow-up reports to proceed with quick online payment of overdue amounts.

Partner payment method

Specify preferred payment methods per partner (incoming and outgoing). You can then filter and group invoices per payment method to create payments in mass more easily. When a payment for a specific partner is created, that partner's preferred payment method will be selected by default.

Payment terms

Added a new payment term date calculation type: "Days end of month on the".

Payments without accounting entries

Payments do not create an accounting move unless an outstanding account is set on the linked payment method.

Peppol

Send invoices on the Peppol network while still receiving your bills in any other system.

PO/Bill matching

Advanced PO matching: A new screen is available to manually match open purchase order lines and vendor bill lines together. You can also create completely new purchase orders directly from vendor bill lines and add lines as down payments on existing purchase orders.

Preferred invoicing method

Define a preferred invoice-sending method and e-Invoice format for contacts to simplify the batch-sending process. Customers can then manage their preferences from the portal.

Send & Print visibility

The visibility of the Send & Print feature has been improved.

Reconciliation models: generate invoice/bill

New reconciliation model types to create a customer invoice or vendor bill directly from a bank transaction.

Reconciliation wizard

The amount is editable when reconciling a single journal item through the reconciliation wizard, allowing for partial write-offs.

Register payment on draft invoices

It is now possible to register payments on draft invoices and bills via the action menu.

Sales taxes price included/excluded

All standard sales taxes now follow a new company setting defining them as price-included or price-excluded, making onboarding and database setup easier. Individual taxes can be forced as being price-included or price-excluded when needed, overriding the default company setting.

SEPA Direct Debit (SDD)

The SEPA Direct Debit (SDD) flow and UX have been improved. Mandates can be sent through a Send & Print action. The pre-notification period is chosen on the mandate and determines when the mandatory notification will be sent to a debtor before a collection. 

SEPA ISO 20022

The SEPA module has been refactored to clearly distinguish between the ISO20022 and SEPA payment methods.

Shared accounts between companies

The same account can now belong to multiple companies, and accounts from different companies can be merged.

Split balance sheet horizontally

Balance sheet can now be presented in two halves next to each other to satisfy various regional display preferences.

UBL invoice import

When importing a UBL (XML) invoice, Odoo will populate the bank account found in the XML on the partner if possible.

Updating imported invoice lines

Add information (e.g., products and tax analytics) to invoice lines imported via electronic invoicing or OCR without impacting the imported information and amounts.

Warning for potential duplicate invoices

A warning is displayed when editing a customer invoice if it is a suspected duplicate of another one by comparing the customer, the date of the invoice, and its amount.

## Localizations 

Argentina 

Accounting: Allow multiple checks when registering a payment. Improved VAT summary with additional details on taxes. The computation of withholding taxes has been automated. Improved withholding support on payment creation.

Australia 

Accounting: Australian taxes have been cleaned up to better suit the market. TPAR taxes are now archived by default. Importers can now manage deferred GST (DGST) entries and related monthly BAS closing easily. Added support for tracking deferred GST. Added new "W" payroll sections to the BAS report. Added new flow to support other amounts withheld (W3).
Payroll: Complete Payroll revamp: Combined all rules in a single salary structure. Updated the new 2024-2025 tax schedule rates. Added the necessary code to submit official compliance tests for STP Phase 2 and SuperStream [Compliance in process]. Completely revamped other input types and super contributions. Added the possibility to import YTD balances for employees transitioning to Odoo Payroll. Completed child support and Medicare levy variation flows. Batch payments directly from a payslip batch, and reconcile them easily. Payroll is linked to Expenses and Accounting for Australian businesses. Termination payments: the whole of tax schedule 7 (from the ATO) has been implemented for unused leaves. Withholding variation can also now also be applied to unused leaves as per variation notice. Added support for Ordinary Time Earnings. Manage both super guarantee and concessional super contributions. Manage workplace giving and director fees.

Bangladesh 

Accounting: The chart of accounts, fiscal positions, taxes, tax report, corporate tax report, and a list of states have been added. 
Payroll: New localization including salary rules calculations, income tax credit handling, and income tax slab calculations.

Belgium 

Accounting: The Individual Accounts report was improved and displays more content.
Payroll: Export work entries to Group S, Partena, and UCM. The end of the notice period is computed more accurately. Automate multifunctional declarations (DMFA) and related changes through the secure file transfer protocol (SFTP) function directly in Odoo.

Brazil 

Accounting: Added the Operation Type to be selected on the invoice or sales order to support more CFOP cases. Implemented e-Invoicing and tax computation for services using Avalara Brazil. Set a default CNAE code on a company or select a specific one on a contact or invoice. Added the PIX payment method to collect payments. The eCommerce module has been adapted to work with taxes and EDI invoice issuance. Create vendor bills from an NF-e XML import. Electronic invoicing for goods sold to end consumers is supported via Avalara Brasil, with related PoS adaptations.

Bulgaria 

Accounting: Added Bulgaria National Bank as an available currency rate provider. Sales and purchase ledgers are now downloadable from the tax report.

Canada 

Accounting: Generate batch payments to vendors (EFT) through a CPA005 file.

China 

Accounting: Added chart of accounts for large businesses in China.

Colombia 

Accounting: Implemented e-Invoicing with DIAN. The eCommerce checkout process has been updated to make it compatible with the DIAN requirements on electronic invoices. Banco de la Republica de Colombia was added as a currency rate provider.

Cyprus 

Accounting: Added the base localization package (chart of accounts, taxes, fiscal positions, balance sheet, profit and loss, and tax report).

DACH

Accounting: The DIN5008 report for the DACH region is implemented in XML instead of Python, increasing flexibility when editing.

Ecuador 

Accounting: Integrated purchase reimbursement flow. Integrated EDI invoice management with the eCommerce workflow. Portal contact information is now editable. Select the withholding base account to be used. Global discounts and negative lines on electronic invoices are supported.

Egypt 

Payroll: Added EOS rules as per the latest updates, tax brackets to include 27.5%, and the value for the minimum taxable amount. Also added a new master payroll report which allows the user to create a sum of the payroll for each month.

Estonia 

Accounting: Updated VAT for Estonia.

Guatemala 

Accounting: Added Bank of Guatemala as a currency rate provider. Support has been added for Factura Especial (FESP), enabling automatic withholding of 100% VAT and ISR, generation of mandatory legal phrases, and FEL certification for purchases where the supplier does not invoice.

Hungary 

Accounting: Implemented e-Invoicing (starting from 17 onwards). Synchronize received vendor bills directly from the NAV API; Odoo automatically fetches and updates received invoices based on the info in NAV.

India 

Accounting: The ENet Payment CSV generator module allows to generate CSV files for vendor payments. Fetch vendor bills, credit notes, and debit notes from the GST portal.
Payroll: Rework of reports. Departments are now available on payslips. 
Time Off: Set a time-off type as sandwich leave.

Indonesia 

Accounting: Added Bank Indonesia as currency rate provider. The payment status via QRIS in the portal is now fetched. Added QRIS QR code to invoices on the portal. Improved e-Faktur range and document management.
Payroll: The localization has been added. 
Point of Sale: Payment statuses for Bank QR QRIS transactions are fetched.

Jordan 

Accounting: Added the base localization package: chart of accounts, taxes, tax report, and the list of states.
Payroll: New localization including basic salary calculations, tax income brackets, national contribution tax, and social security.
Point of Sale: Generate JoFotara-compliant receipts.

Kenya 

Accounting: Now compliant with the new Kenyan ETIMS system, including the OSCU checklist.
Payroll: The master report has been added.

Korea 

Accounting: The list of provinces and metropolitan cities has been added.

Kuwait 

Accounting: The chart of accounts has been added.

Luxembourg 

Payroll: Rework of the localization, improving monthly computation and gratification. Support for CIM credit has been added.

Malaysia 

Accounting: Implemented e-Invoicing with Peppol PINT Malaysia. Added the statement of account report. Added Bank Negara Malaysia as a currency rate provider.

Mauritius 

Accounting: The base localization package was added: taxes, fiscal positions, and tax report.

Mexico 

Accounting: Improved XML invoice import: withholding taxes support, improved bill duplicate detection using the Folio Fiscal ID, invoices are marked as "to check" when a potential problem occurs during the import (such as bad tax detection). Simplified Addenda management. Implemented delivery guide update 3.1 (available from Odoo 15). You can edit the payment way to declare in the CFDI payment complement directly on a bank transaction (without needing a payment in between). Cuenta Predial values added on products are included in the products' CFDIs. Configure and add local taxes in the CFDIs generated in Odoo. The RegimenFiscal, if specific to a branch, is taken into account when emitting a CFDI, even if the certificate and the RFC number belong to the main company. The Complemento de Pago payment cancellation flow with replacement has been improved. Added the Complemento de pago generation for generic customers.

Morocco 

Accounting: Export the tax report in XML format. The ICE (Identifiant Commun de l'Entreprise) is now displayed on invoices to ensure compliance with Moroccan regulations.

New Zealand 

Accounting: You can now pay NZ suppliers faster by downloading batch payments in the EFT Batch Transfers format for the following banks: Westpac, BNZ, 8ANZ, ASB. Added Remittance Advice Report. Configure a start date for the fiscal period that is different from January 1st.
Payroll: The Employment Hero integration is now available in New Zealand.

Nigeria 

Accounting: The base localization package was added: taxes, fiscal positions, tax report, and withholding tax report.

Pakistan 

Accounting: The existing withholding taxes have been improved and new ones introduced. Two new tax reports are also available and the list of states has been added.
Payroll: New localization including basic salary calculations, tax brackets, and tax deductions. 

Peru 

Accounting: Added the detraction amount to the invoice's PDF report. Integrated EDI management with the Point of Sale order and eCommerce order workflows. The PLE 12.1 and PLE 13.1 inventory reports have been added for detailed inventory tracking. The PLE 1.1 and 1.2 reports have been added to track all cash transactions and current account operations, including inflows, outflows, deposits, withdrawals, and transfers. Support has been added for 19 sub-books of the Peruvian Inventory and Balances Electronic Book, including Trial Balance, Cash Flow, and specialized account reporting for SUNAT compliance.

Philippines 

Accounting: Added new 2550Q (2023 version) tax report and SLSP report. The new standard check print layout has been implemented (available from 17.0). Withholding taxes and support for SAWT and QAP reports have been added.

Qatar 

Accounting: The chart of accounts has been added.

Romania 

Accounting: Implemented the E-factura sending to SPV, as well as the flow required to get Access Tokens from ANAF to use the web services. When downloading an invoice from ANAF, if the XML does not include a PDF, Odoo will download the official ANAF-generated PDF and use it as the attachment. Generate the SAF-T with Stock variant from the general ledger report. If an invoice is rejected by the SPV, it can now be reset to draft, corrected, and sent again.

Rwanda 

Accounting: The base localization package was added: chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, and tax report.

Saudi Arabia 

Payroll: Added a new master payroll report which allows the user to create a sum of the payroll for each month. The end-of-service calculations have been improved, and the GOSI calculations have been updated. Generate the SIF file as part of the Wages Protection System payroll process.
Point of Sale: The ZATCA PDF is no longer generated during order validation, avoiding unnecessary waiting time. It can be generated on demand when the invoice is first viewed or downloaded.

Singapore 

Synchronize your journal entries to Odoo from Employment Hero (previously KeyPay).

Spain 

Accounting: Added the possibility of filling in Invoicing Periods and changing Payment Means on invoices for Factura-e. Send vendor bills to the Batuz Tax Agency. Added new contact types on contacts for Factura-e Administrative Centers support. Added AEAT modelo 130. The Modelo 303 has been updated with the Q3 2024 changes from the AEAT. The TicketBAI implementation has been updated and is triggered on Send&Print.
Point of Sale: TicketBAI allows to send Factura Simplificadas directly from sales orders without having to create separate account moves.

SYSCOHADA

Accounting: Each of the 17 member countries of SYSCOHADA now has its own localization module. They all have their own taxes and tax report configured while having the common OHADA chart of accounts. The new NPFE-specific chart of accounts and industry-dependent reports have been implemented based on the SYCEBNL referential.
Payroll: New localization supporting basic calculation, tax income brackets, national contribution tax, and social security.

Tanzania 

Accounting: The base localization package was added: chart of accounts, taxes, fiscal positions, tax report, balance sheet, and profit and loss report. 

Thailand 

Accounting: The Bank of Thailand has been added as a currency rate provider.

Turkey 

Accounting: A default return from sales account is now defined in the sales journal and product form. New taxes and a new tax report have been introduced. 
Payroll: New localization including social security premium/insurance calculations for employment and unemployment added to the salary rules; income tax calculations added, as well as stamp tax deductions for more accurate taxation results.

Uganda 

Accounting: The base localization package was added: chart of accounts, taxes, fiscal positions, and tax report.

United Arab Emirates 

Accounting: The corporate tax report has been added.  Configure a start date for the fiscal period that is different from January 1st.
Payroll: A new master payroll report has been added. It allows calculating a sum of the payroll for each month. Generate the SIF file as part of the Wages Protection System payroll process. The salary rules have been updated to include provisions, social insurance, DEWS, out-of-contract days, remaining leaves balance payments, other inputs for bonuses, arrears, and other allowances. The EOS calculation has also been updated to take into consideration the free zones' calculations. The payslip printout formats have been updated, and the end-of-service printout format has been introduced.

United Kingdom 

Accounting: Configure a start date for the fiscal period that is different from January 1st.

United States 

Accounting: Companies can print their checks on blank check paper directly from Odoo without relying on a third party to pre-print their format. The tax created using the Avatax module features shorter tags and uses tax groups to improve the display. Added a compatibility module between the Amazon module and the Avalara tax computation.
Payroll: Display the accrued time in the payslip period on the printed payslip. Alabama, Nevada, Washington, and Colorado are now covered, including the workers' compensation for Washington State. Generate a CSV file with an overview of the hours worked and time off to import into ADP.

Uruguay 

Accounting: Base localization package configuration updated (chart of accounts, LATAM module dependencies added). The DGI e-Invoicing via Uruware was implemented.

Vietnam 

Accounting: The tax report has been updated. Integration with SInvoice for e-Invoicing has been implemented. The chart of accounts and balance sheet have been updated following Circular 99/2025/TT-BTC on corporate accounting guidelines.
Point of Sale: E-invoices can now be issued via SInvoice for POS orders, ensuring compliance with local tax regulations.

Zambia 

Accounting: The base localization package was added: chart of accounts, taxes, fiscal positions, balance sheet, and profit and loss report.

## Appointments 

Appointment types: chatter and template

The appointment type form now features a chatter.
Load an appointment type template to get started quickly.

Booking status

Manage your bookings' status (requests, booked, no-show, etc.).

Confirmation customization

Customize your Appointments page to hide details you don't need such as timezone or duration.

Conflicting meetings warning

Get a warning when an attendee already has a meeting planned, even if the Employees on Appointments module is not installed.

Duplicate bookings detection

Visitors get a warning when booking a potential duplicate meeting.

Dynamic snippet

Specific appointment types are now showcased in a dedicated snippet.

Instantaneous confirmation emails

Appointment confirmation emails are sent instantly after the booking is confirmed.

Payment by seat quantity

Allow customers to pay based on the number of seats they book.

Share availability

Configure your custom availability link in a simplified form.

## Appraisals 

Multi-company employee management

You can now manage employees and appraisals in a multi-company environment.

360 feedback

Resend the request for 360 feedbacks for multiple employees at once.

New notification option

Choose to follow an appraisal and to get notified of 360 feedback answers.

New onboarding screen

Demo data has been improved to remove code complexity and multi-app usage errors.

Request hierarchy

The creator of the appraisal, in case of higher management action, is automatically added to the appraisal.

## Approvals 

Filters

Added new filters and "group by" options.

## Attendances 

Extra hours validation

Validate all or partial values of the extra hours worked.

Automatic check-out and missing hours

The settings for automatic check-out and absence management have been added.

Improved UX

Revamped multiple UX aspects.

## Barcode 

New design

A new design improves the user experience.

Create product with barcode lookup database

Scanning a new product that belongs to the barcode lookup database prompts creating a new product complete with editable information from the barcode lookup database.

Group batch lines and disable add product

Group batch lines of the same product. Disable adding unwanted products in picking.

Hide quantities

It is possible to hide supposed quantity on inventory adjustments.

Hide reserved lots and serial numbers

It is possible to hide reserved lots and serial numbers on transfers.

Multiple serial numbers scan

Generate QR codes containing all the serial numbers contained in a package and scan them on receipts to record lots and serial numbers en masse.

RFID support

Use an RFID scanner for superfast receipts and inventory adjustments of products with RFID labels.

Scan transfers

Filter batches by scanning a transfer, a product, or a package.

Serial numbers and lots improvements

It is possible to scan a lot or serial number from the Barcode main screen to show its details.

Sign delivery

Companies who make their own deliveries can now sign these deliveries by scanning a barcode.

## Calendar

Default appointment duration

Define the default duration of an appointment by saving it as a user-defined default.

## CRM

Expected revenue

The expected revenue for leads is updated upon quote confirmation.

## Dashboards

Manage dashboard sharing

Publish dashboards to make them visible to others or keep them private.

Company-specific dashboards

Define a company for your dashboards.

New dashboards

The following dashboards were added: Purchase & Vendor analysis, Operation analysis, POS - Restaurant, Warehouse Daily Analysis, Warehouse Metrics, and Email Marketing.

## Discuss

@everyone mention

Ping all channel members at once without mentioning them individually.

Activities: list view and mass actions

Go through the activities of the day thanks to a comprehensive list view and manage them using the list view buttons.

Activity plans: timing

Define the timing of each activity of an activity plan.

Actual chatter in portal

Portal users can now interact more easily: add reactions, copy message links, and edit or delete their messages directly in the portal's chat.

Breadcrumbs

Go back to the previous view when opening Discuss thanks to the breadcrumbs.

Canned responses

You now need to manually select the canned response for it to be inserted. Canned responses can be private or shared.

Chat window

Minimized chats appear as bubbles in a more natural order.

Configuration improvements

Improved notification management through the configuration and adjustable default settings through the general menu.

Copy message link

Share the content of a message from another thread via a link.

Emails: dynamic placeholders

Dynamic placeholders can be inserted in emails using a dedicated button.

Heavy attachments

In order to prevent email servers from refusing emails, heavy attachments are now turned into links.

Mail composer

The mail composer has been improved.

Message scheduling

Schedule messages to be sent later.

Mobile push to talk

In a call's settings, choose from Voice Detection or Push to Talk options.

New message separator

Easily identify new messages with a separator that appears above the last unread message.

Persistent composer

Stop losing the content of your messages when switching from the full to the small composer of the chatter due to a network connectivity issue or accidentally clicking the close icon.

Sub-threads

Sub-threads have been introduced to enable secondary discussions within a channel.

## Documents

Attachment smart button

The document smart button connects accounting records to related documents.

Automated document deletion

If Files Centralization is enabled, deleting a record will send its attachments to the trash in Documents.

Full resolution images

Full resolution is kept when uploading documents.

Hotkeys in Split PDF tool

Use hot keys to perform actions in the Split PDF tool.

Preset splits and selection

Use SHIFT S in the Split PDF tool to split the doc on blank pages and remove them.

Workspace sharing

You can now share a workspace from the workspace navigation panel.

Workspace, rights & share management

The app has been revamped with a new design and new rights, share, folder, and action management.

XML files with an embedded PDF

Preview XML files containing an embedded PDF.

## eCommerce

Attribute selection follows users

If you apply filters on the shop page of your eCommerce, the correct variants will be selected when navigating to your product pages.

Click & Collect

The new Click & Collect feature allows customers to check the in-store stock and pick up their order at the store of their choice.

Delivery form moved to checkout

Users can now select their preferred delivery method during the delivery stage of the checkout.

Display product.template

Show product templates instead of variants in the "Products" building block, if desired.

Configure products

eCommerce products can now be entirely configured from the backend.

Mega menus

Build your mega menu based on eCommerce categories.

New interface for pick-up points

A new pick-up point interface is now available for both SendCloud and Fedex.

Customizable category pages

Customize product category pages with building blocks.

Optimized product images

Product images are converted to the WebP format on the website when uploaded from the backend.

Product ribbons for variants

Create ribbons easily and apply them exclusively to product variants, not just product templates.

Save for later

Customers can now move products to their wishlist directly from the cart.

Shop display options

Decide whether all users or only those who are logged in can access your /shop page. If the latter is chosen, unlogged users won't have any visibility into your product catalog.

Sign in/up button visibility

When unlogged customers place an online order, they must first enter their email address and have the option to sign in directly. If enabled in the website settings, they can also subscribe to your newsletter at this step.

## eLearning

Sequences on additional resources

The order of the additional resources added to a course's content is now kept on the front end.

## Email Marketing

Opt-out reporting

Use the opt-out report to see why subscribers unsubscribe.

Optional contact name split

Split mailing contacts into a first and last name.

Recipient clicks

See which recipients clicked links in mailings.

Tracker granularity

Track multiple links leading to the same URL and see which one gets the most clicks.

## Employees 

Activity plan and UX improvements

Activity plans for employees now handle unlinked managers more efficiently. The UX has also been improved, with a new Payroll tab, support for legal names, etc.

Attendance management

The Advance presence control settings have been reworked, and the Presence report has been removed, with all its options now available from the Employees' Kanban view.

Avatar card preview

You can now click the avatar card in the Planning, Timesheets, and HR apps to get a quick overview of your resources' roles and skills.

Back from leave date smart button

View when employees are back from leave using a dedicated smart button.

Bulk signature requests

Request signatures from a selection of employees in list view.

Department manager visibility

Managers can view reports and information concerning their teams without needing additional access rights in HR.

Organization chart: new filters

New filters have been added to the organization chart.

Employees' bank accounts

Manage employees' bank accounts from the Employees app using a dedicated menu.

Skill colors

Define colors on skill categories.

## Events 

Events from Point of Sale

Sell events from Point of Sale.

Pop-up register button

Attendees can now register from any page.

Retroactive lead creation

Retroactively create leads from your event attendees.

Visibility

Allow everyone, only logged-in users, or anyone with a link to register for an event.

## Expenses 

Add vendor for expenses paid by company

Employees can now specify the vendor of expenses paid by company to improve information completeness and ease the bank reconciliation process.

Create draft entry after approval

Entries are created in draft after manager approval rather than being created and posted simultaneously at the "Post" stage. This gives greater flexibility to the accountant, who can manage everything from the Accounting app, and it simplifies the synchronization of statuses between Expenses and Accounting.

Report in next payslip improvements

The interoperability between the Expenses and Payroll applications is improved. Posting the miscellaneous salary entry triggers the expense vendor bill creation, post, and reconciliation. The vendor bill is immediately reconciled with its payable counterpart in the miscellaneous entry and is marked as paid. Moreover, users can now remove expense reports from draft or cancelled payslips.

## Field Service 

Gantt view

Easily dispatch unassigned tasks from the Gantt view when filtering for specific users.

Pick up products

View the products to pick up for field service tasks through a smart button.

Plan by location

Group field service tasks by location to plan efficient travel between job sites.

Simplified mobile view

Navigate tasks easily with a simplified mobile form view for onsite workers.

Skills

Assign tasks to the right people based on their skills.

## Fleet 

Contract UX uniformization

Changed the vehicle stages and made various usability improvements.

Mass email drivers

Send an email to a selection of drivers using the mail wizard.

## Helpdesk 

Forum improvements

Create helpdesk tickets from forum posts.

## Inventory 

Batch and wave picking improvements

Automate wave picking creation. When grouping transfers into waves/batches, the elements common to all transfers, such as contact or location, appear in the wave/batch's description.

Create backorders in advance

Split pickings before validating, allowing deliveries at two different locations or dates.

Cross-company lots/serials

Lots and serial numbers are now fully traceable through multiple companies. When doing an inter-company transfer, serial and lot numbers remain.

Dashboard UX

Revamped the Inventory dashboard UX.

Dispatch management system

Organize delivery rounds and manage shipments with your own fleet or third-party logistics.

Eased return process

When creating a return, create a new transfer to send new products back.

Editing a package's location

Change the location of a package without creating any transfer and move packages using the Kanban view.

Editing layer values

Edit specific valuation layers for revaluation.

Inventory reports UX

Inventory reporting menu revamp.

Make to stock and order

When a rule is set as "Take from stock, if not available, trigger another rule", launch a procurement for only the quantity missing from the new move.

Manual entry of barcodes

You can now enter barcode numbers manually.

Next transfer button

A new smart button has been added to transfers to show the next linked transfers.

Pull to push rules and flexible routes

Push rules are now triggered when a transfer is validated. Transfers are not created in advance, allowing more flexibility. Transfers waiting for another transfer are no longer polluting the space. All default multi-step routes have been redesigned to reflect this change.

Putaway rules

Improved putaway rules put incoming products where other quantities of the same product are or have been.

Tracking at delivery

To ease the transfer of tracked products that are not tracked at reception, they appear as non-tracked products in transfers.

User rights: inventory adjustments

Users can now apply inventory adjustments.

Valuation by lot/serial number

Each lot or serial number of the same product now has a separate valuation.

View empty locations

See which locations are empty from the locations list view.

## Knowledge 

Article organization

Browse articles using a tree that can be expanded and collapsed.

Calendar start/end display

Define the start/end time of the calendar in an article.

Hierarchy navigation

Locate the article you are reading with the new top bar.

Search full text

Search inside Knowledge articles through a new search interface.

Shared articles

When a published article is shared, the "public article" navigation panel is no longer displayed.

## Live Chat

Discuss and command palette shortcuts

Join or leave Live Chat channels directly from the Discuss app or the command palette.

## Maintenance

Property fields for maintenance tasks

Create property fields for maintenance tasks.

## Manufacturing

Master Production Schedule

The design has been updated. The following features have been implemented: yearly planning, automatic replenishment mechanism, maximum batch sizes for manufacturing replenishments, and import template. When accessed from a bill of materials, the MPS is available across all BoM levels for full visibility.

Mass production of serial numbers

Specify component consumption in the revamped mass production wizard.

Work in progress

Post work-in-progress journal entries for ongoing productions to record material and labor consumption in the balance sheet.

BoM Overview

Availability now also takes into account work center availability.

## Marketing Automation

Activity summary

An interactive summary explains plainly what the activity will do.

Choose server

Select which mail server is used for automation campaign mailings.

Templates for e-Commerce flows

Marketing automation templates have been added for e-Commerce flows.

WhatsApp message as an activity

Schedule WhatsApp messages for your Marketing Automation campaigns.

## Marketing Card

New social app

Promote events through community marketing, and track speakers, attendees, and sponsors.

## Meeting Rooms

Full screen mode

The Meeting Rooms app now supports full-screen display.

## Online Payments

Display unavailable payment methods

Payment forms now display unavailable payment methods to internal users in debug mode for them to better understand why a given payment method is available or not.

Razorpay

Razorpay now supports tokenization. Razorpay now supports EMI plans as a payment method.

Removed deprecated modules

The Alipay, PayU Latam, and PayUmoney payment providers have been removed.

Xendit

Tokenization is now supported for Xendit. Xendit now supports payments in Malaysia, Thailand, and Vietnam

Mollie

The "Twint" payment method has been added.

Worldline
Worldline is available as an online payment provider for Europe. It replaces the Ogone and SIPS payment providers.

PayPal

PayPal is integrated with a new payment flow allowing customers to pay without leaving Odoo.

## Payroll 

Automatic localization installation

The localization is automatically installed if it exists for your country.

Dashboard warnings

The dashboard warnings now include additional options.

Flexible number of roles

It is now possible to have more than two roles that must sign a contract.

Generate payslips

The Generate payslips wizard has been reworked for a better selection.

Headcount

View your headcounts based on contracts at any given time.

Offer smartbutton

Navigate between signed offers and contracts with a smartbutton.

Payslip template

Define a payslip template structure, with new styles available for both the classic and light templates.

Rule condition/computation based on inputs

Define salary rules with the "Other Input" condition/computation type.

Salary Attachment Types

Salary Attachment Types have been combined with Other Input Types.

Salary configurator: bicycle categories

Fleet: in the salary configurator, bicycles are displayed in categories.

Search salary rules by code

Search salary rules by python code which will search into the rules conditions and computation code.

Title, job position, and department

The employee's title, job position, and department are only updated at the start of their new contract.

Uniformization of payment report

A generic payment wizard is available across localizations. It is possible to generate a CSV file for every case.

Year-to-date computation

Set the year-to-date computation on payslips, depending on the structure, with a customizable end-of-year date.

## Planning 

Consider availability

Prevent scheduling shifts on days when employees are unavailable to work by considering availability when generating recurring shifts.

Flexible working calendars

Configure fully flexible work schedules for employees, with no fixed daily hours, weekly hours, or designated working days.

Gantt color-coded hour analysis

Quickly identify under- or over-assigned hours in Gantt view by grouping employees.

Integration with Rental

When a sales order with a rental product configured to plan services is confirmed, the shift is automatically scheduled for the rental period.

Shift unsplitting in the Gantt view

Unsplit a shift in the Gantt view with a single action.

Undo "Auto Plan"

Undo the "Auto Plan" action to instantly remove all automatically scheduled open shifts and sales orders.

Shift unscheduling

Shifts can be unscheduled from the Gantt view using a dedicated button.

## PLM 

UX improvement

Specify whether the engineering change order (ECO) updates the version of the product or bill of materials. A dedicated section was also added for work order instructions.

## Point of Sale 

AvaTax

AvaTax is fully integrated into PoS.

Cash difference logging

Opening and closing counts of the cash register are now always logged in the PoS Session record.

Create products in PoS

Creating and editing products is now possible directly from the Point of Sale application.

Customer display

The customer display can now be used on any remote device without needing an IoT box.

Editable payment methods

Payment methods are now editable before printing the receipt.

PoS redesign

Fully redesigned the Point of Sale front end with a focus on simplicity and user experience.

QR code payments

Customers can now pay using their bank application.

Restaurant: predefined internal notes

Define frequently used internal notes (e.g., "No dressing" or "Emergency") to be added to orders with the click of a button.

Restaurant: table merge

Quickly merge tables to centralize orders, e.g. for big groups of people.

## Project 

Allocated hours in the Gantt progress bar

Employees' workload is now displayed in the Gantt view progress bar, based on task time allocation.

Analytic plans on projects

Get clear financial reports by assigning project costs and revenues to different analytic plans. Use analytic entries to track landed costs and track costs/revenues from stock pickings. Re-invoice delivered products and configure products to automatically create tasks in the projects specified in your quotations.

Burn-up chart

Get a burn-up chart analysis for your projects

Consider deadlines

Automatically consider deadlines when scheduling tasks in batches from the Gantt view.

Display deadlines in Gantt view

View at a glance the start and end dates of a project from the Gantt view of its tasks.

Kanban view: sub-tasks creation

Create sub-tasks from the Kanban cards of your tasks

Optimized task hours

The hours allocated for your tasks are now based on their planned dates.

Project sharing

Portal users can now be granted edit access to all project tasks or only the ones they follow. With full edit access, they can choose which communications to follow. They can also @mention other followers in tasks.

Top bar to browse related project records

Navigate to related records like timesheets and invoices from the task Kanban view using embedded actions in the control panel. Add custom actions, and choose to share them or keep them private.

Tracking revisions in tasks

Monitor task description changes and revert to previous versions.

## Purchase 

Amounts in local currency

View totals in company currency on purchase orders and compare alternatives using company currency.

New purchase agreement type

Added a new type of purchase agreement to create requests for quotation with fixed quantities easily.

RFQ management from email/portal

Customers can accept or decline requests for quotation from an email or the customer portal.

Tracked products: generate lot numbers

Generate lot numbers when receiving tracked products.

## Quality 

Quality checks with spreadsheets

Use spreadsheets for quality checks.

On-demand control frequency

The on-demand control frequency type has been added for quality control points.

Reporting

Improved reporting for quantity passed/failed. 

## Recruitment 

Access rights

Recruitment access rights no longer affect Employee access rights.

Duplicated applications management

Display information about duplicated applications on the website. Recruiters can refuse all duplicates at once.

Email parsing

An email parsing menu defines new parsing rules that allow extracting the applicant name from generic job boards application email.

Job forms: conditional questions

Fields in a job form can now be displayed only for a specific job description.

Jobs section

The jobs page of your website is now included in the menu.

Mass email templates

Use an email template when sending an email to multiple applicants at once.

Monster integration

Post job positions on job boards such as Monster directly from Odoo.

New Candidate model

A new Candidate model has been introduced to help process multiple applications at the same time.

Referral campaigns

Initiate a referral campaign for a job position from the recruitment app and send it to a set of employees.

Refuse an offer

In the salary configurator, use the feedback button to discuss with the recruiter. In Recruitment, refuse an offer and provide a reason.

Salary configurator states

States in the personal info section of the salary configurator are related to selected country.

Skills on job position

Match the skills of a job position with applicants of the pool to find suited applicants.

## Referrals 

Refused referees filter

Added a filter to see refused referees.

Send by SMS

Send referral links to job positions by SMS.

Send by Whatsapp

Send referral links to job positions by WhatsApp.

## Repairs 

Improvements

Quality checks are now also available for repair orders. Specify different locations for the final product and components.

## Sales 

Shopee integration

Fetch orders and delivery slips, and sync inventory levels with the Shopee integration.

Amazon connector

A free order is now created when a product is replaced by Amazon.

Attribute values removal

You can now delete attribute values that are no longer in use, even if they were previously used in some products (you must remove them from those products first).

Combo products

Combo products are now available in the eCommerce and Sales applications. They allow combining several products into a single one with the possibility for customers to pick from multiple choices.

Commission plans

Manage the commission plans of sales teams.

Custom zones in PDF quote builder

The PDF Quote builder is now more flexible. Choose when to use it, which documents to use, and insert text specific to each quote in the PDF.

Customer view notification for quotations

Salespersons can decide if they want to receive a notification when the customer views the quotation.

Dynamic sale description from quotation template

Provide product descriptions in the quotation template to use them in quotations created from that template.

EDI for orders

Drag and drop your customer's purchase order to create a new sales order. If the purchase order comes from Odoo or contains the correct XML information, the order will be prefilled.

Gelato

Connect Odoo with Gelato, a print-on-demand service.

Manage parent categories

Child categories are included in global filters.

Portal: loyalty cards and eWallet

Customers with access to the customer portal can find and consult the balance of their Loyalty cards and eWallet.

PDF quote builder improvements

Any field of the model (even customized ones) can now be used in the PDF quote builder.

Pricelist revamp and print formats

Pricelist views and usages have been simplified.
Pricelists can now be generated in PDF, CSV, or XLSX format.

Product documents on email templates

The "Send quotation" and "Order confirmation" email templates have been adapted to mention product documents available for the customer in the portal.

Product form view improvements

The product form view and model has been reworked. Storable and Consumable product types have been replaced by Goods, and the Tracked field determines if and how the product is tracked.

Product prices

Product prices modified on sales order lines are no longer recomputed when the quantity is changed.

Product updates for attribute changes

Update all products when adding an attribute value or changing the extra cost of an existing value.

Product descriptions

Products and descriptions are combined in a single column when editing sales order lines.

Quotation templates sequence

Sort quotation templates by order of importance.

Removed deprecated modules

The eBay Connector has been removed.

## Shop Floor 

UX improvements

Add operations in any order and add notes. Instruction steps are folded by default.

## Sign 

Default sign template

Select a default sign template on your activity type.

Document signature request

Request a signature from any document in Odoo and be notified when it is signed.

Optional reminders

Control signature reminders and disable them in a single click.

Radio buttons

Radio buttons can now be added to documents from the Fields list.

Signed PDFs: bookmarks and settings

Bookmarks and relevant PDF settings, such as metadata, are preserved from the original uploaded document in the final signed PDF.

## Social Marketing 

Post customization by network

Customize your content by splitting posts across networks to better suit each audience.

Image-only posts

Publish posts only containing an image on social media.

Instagram: multiple image posts

Post multiple pictures in a single Instagram post.

New LinkedIn API

The new LinkedIn API allows editing comments, liking and deleting posts, and inserting images as comments.

## Spreadsheet 

Pivots: measures

Edit and add measures in pivot tables.
Select the way measure values are displayed in the pivot table.
Measures can also be renamed in pivot tables.
Pivots can use the same measure multiple times with different aggregators.

Pivots: dimensions

Edit rows' and columns' dimensions when using pivot tables.

Dynamic pivots

Pivot tables are now inserted as dynamic pivots in a spreadsheet, and an option to switch from a dynamic to a static pivot has been added.
You can also define the number of columns to display in a dynamic pivot.

Pivot measures and groups

Measures and groups can now be customized on pivot tables.

Pivot creation

Create pivot tables from scratch in a spreadsheet.

Copy/paste pivot table cells

Copy-pasting a cell or a range of cells from a dynamic pivot will now paste the static pivot formula for each copied cell(s).

Pivots: dates

Refer to a date in a pivot formula, regardless of the date format. The date format expected in the formula will be inferred automatically.

Pivots: calculated fields

Create calculated fields. Add extra dynamic measures by adding calculations (i.e., calculation between fields from the pivot, between values located elsewhere in the spreadsheet, or a mix of both). Functions can also be used in those extra measures.

Pivots: increased date granularity

Added day of the week, hour of the day, minutes, and seconds as options for date granularity.

Tables for array formulas

A dynamic table adapts to the formula's output, allowing handling complex datasets.

New functions

The SEQUENCE, INDIRECT, OFFSET, CONVERT, and CELL functions have been added.

PIVOT formula

ODOO.PIVOT formulas are renamed PIVOT.

Vectorization on formulas

It is now possible to use a range of cells in formulas that request a single argument. The returned value will then be an array of cells.

Paste values, preserve format

Number formatting is now preserved when pasting values.

Plain text format

Added plain text format.

Remove source spreadsheet when adding to dashboard

The original spreadsheet of a Dashboard is now send to Trash in Documents.

Insert list selection

Insert a selection of records from a list to a spreadsheet.

Unused data sources

Unused data sources are flagged in the Data menu.

Find and Replace feature

Added search granularity (all sheets, current sheet or specific range) to the Find and Replace feature.

Data tables

Data tables allow quick sorting, styling, and filtering of data as well as adding new records.

Cell comments and tags

Insert comments and tag someone on cells.

Tables autofill

Adding a formula in a new column autofills the whole column.

Partial VLOOKUP

Support of partial match in VLOOKUP formulas.

Import/export groups

Groups are now included when importing or exporting data.

Automatic expansion

Multi-cell formulas now adjust range for results.

Enhanced gauges

Improved gauge charts visuals.

Checkbox and dropdown

Insert checkboxes and dropdown lists.

Version history

Restore a previous version from the version history panel.

Chart customization

Added design features in the chart editor.

New chart types

New chart types have been added (area, combination, horizontal bar, doughnut, population pyramid, scatter, radar, and waterfall).

Pie charts

Pie charts exclude negative numbers.

Chart data values

Choose to hide or display data values on charts.

Scorecards design and readability

When the baseline description is too long, its font is reduced to fit on one line.

Custom formats detection

When a specific format (date, currency, etc.) is detected it appears on the top bar.

Date formatting: quarter

Dates can be formatted as quarters by using the "more date formats" menu. The quarter recognition has also been improved in pivot tables so that any dates can be used in pivot formulas.

Highlight and hover

Related cells are highlighted when editing or hovering over pivot/list data sources.

Accounting format

The Accounting format has been added.

Conditional formatting: Data bar and formulas

The Data bar conditional rule format has been added.
Use a formula as a condition for formatting cells.

Copying data to another spreadsheet

Values can be copied and pasted from one spreadsheet to another (excluding Pivot and List formulas).

Custom table style

Create custom table styles.

Global filters: domain edition

Define a domain to restrict the list of values.

List sorting

Modify the list sorting from the side panel.

Sheet tab colors

Assign colors to sheet tabs.

Spreadsheet quote calculator

Link a spreadsheet template to a quotation template and access your calculator from a sales order. Calculated values in the spreadsheet can overwrite the initial values in the sale order.

Viewing records from charts

A "See records" option is available when right-clicking a data series. The "Link to Odoo menu" has also been removed.

Table resizing

Adjust the size of the table using a button.

## Studio

Columns in tables

Add columns in tables with Studio's report editor.

Data export

Export data and demo data with Studio.

Edit buttons

Change the label of buttons in form views with Studio and highlight them as primary or secondary buttons.

## Subscriptions

Align subscription invoicing periods

Configure recurring plans to align all subscription invoices on the first day of each billing period.

Automate future payments

When customers pay an invoice related to a subscription with a payment method that can be saved, a checkbox offers the ability to automate future payments.

Invoice on delivered quantity

Support invoicing subscription products at the end of the period with an invoicing policy based on delivered quantity.

Payment with non-tokenizable method

It is now possible to pay subscriptions using payment methods and providers that do not support tokenization.

Price update and email sending

Notify customers of price updates or changes to subscription terms and batch edit subscription item prices.

Recurring plan on eCommerce

Customers can select a recurring plan on the ecommerce product page.

Subscription cancellation timing

Define whether a subscription should be closed immediately or at the end of the current period when a customer cancels their subscription from the portal.

## Surveys

Live session QR code

Display a QR code to join a live session.

Rewards for quick answers

Improvement of the "rewards for quick answers" option.

Access restriction

Restrict access to sensitive surveys and assign multiple people to manage surveys with the "Restricted to" field.

Scaling question type

Added a new question type: scale.

## Time Off 

Accrual expiration date

Add an expiration date on carried-over days off.

Calendar: working hours

Employee availability is displayed according to their working hours.

Duration hours in reporting

Reports are now displayed in days and hours.

Leave approval

Approve a leave directly from the overview screen.

Remove draft state on leave

The draft state has been removed on time off requests.

Remove multi-employee leaves

The way multi-employee leaves and allocations are created and managed has been changed.

Time off policies

Public holidays can now be included/excluded on specific time off types.

Time off type visibility on employee dashboards

Choose if a time off type should appear on employee dashboards.

Warning message

Improvement of the warning to use days before end of an allocation.

Yearly accrual cap

Add a yearly maximum cap on a milestone for an accrual plan.

## Timesheets 

Grid view: spot missing hours

Quickly identify under-assigned hours in employees' timesheets using the Grid view.

Weekends in the grid view

Toggle weekend visibility on and off in the grid view.

## Website 

Blog posts: improved layout

Additional layouts have been added for blog posts.

Custom page templates

Add any of your pages to the new page wizard so they can be used as a template.

Default color palette

The color palette has been updated to reflect current design standards, ensuring a more vibrant and up-to-date appearance.

Document link

Create direct links to documents so you can use them anywhere on your website.

Google Consent Mode v2 and CMP

Cookie management and new Google Consent Mode V2 rules were implemented.

Header layout

Customize the layout of the header for blog, event, and eLearning pages.

Import existing website

Convert any existing website into an Odoo one in just a few clicks, even for existing databases.

Loading design

The website loading design now features smooth transitions and a clean progress indicator.

Pastel palettes

New pastel color palettes are available in the theme section.

Property fields in website forms

Use property fields in forms on your website.

Shapes animation speed

Customize the animation speed of background shapes.

Snippets library

Added 60+ new snippets to the library. Enhanced the snippet selection by showing actual previews. The discovery system will help users choose the right snippet thanks to a keyword selection. Users can also replace a dropped snippet with another one.

Theme preset: colors gradients

Select color gradients for your backgrounds in your color preset.

Theme redesign

Our 27+ themes have been redesigned to use our new snippets and new color palette.

Theme selection

The theme selection page has been improved.

Upload custom fonts

Import your own font families to enhance your website.

Video lazy loading

YouTube and Vimeo content are now lazy-loaded to improve the website overall speed.

Website builder

Choose from the whole theme library when building your website.

## WhatsApp 

Automated notifications

Send WhatsApp messages via server or automated actions.

Centralized chat history

All WhatsApp conversations with a contact can be accessed from the contact's form.

### Odoo Experience on YouTube

 1. Use the live chat to ask your questions.

 2. The operator answers within a few minutes.

 Watch now
