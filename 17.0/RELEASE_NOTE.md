# Odoo 17

## Release Notes
 Try it now

## Accounting 

Accounting reports

Configure accounting reports more easily: move entire sections, define the parent line by dragging and dropping, and add indents to highlight the hierarchy.

Allocation of invoice discounts

Separate sales and granted/received discounts on invoices by posting them on different accounts. The sales lines remain unaffected, but the discount is subtracted and moved to the specified account.

Assets usability

Assets import usability was improved. It is now possible to post assets in bulk. The status of an asset can't be imported anymore.

Auto extract PDFs only

Prioritize PDF attachments when using the 'create invoice on email' feature for sales and purchase journals.

Auto-reconcile feature

Introduction of a new wizard for auto-reconciliation.

Avalara geolocalization and sales tax

Using Avalara's address validation now populates geolocalization information on the partner. This information can be used to compute sales taxes.

AvaTax: use warehouse address in ship from information

When computing taxes with AvaTax on an invoice linked to a sales order with deliveries and pickings, Odoo now uses the warehouse address where the goods are shipped from to compute the most precise tax possible.

Bank reconciliation layout

The bank reconciliation system has a new, clearer widget layout. Edit, delete, and print bank statements from the widget. Dashboard links are improved, and audit features can be found in the journal items view.

Bank statement PDF report layout

The layout of the bank statement PDF report has been cleaned up.

Bills artificial intelligence

Tax and account prediction on vendor bill lines are now always activated. Product prediction can be activated in the settings.

Branches management

Manage business units/branches thanks to multi-company hierarchies.

Credit and debit note buttons

Simplified the invoice action buttons. Debit notes was moved to the action menu.

Cross analytic

Input on multiple analytic plans to do analytic cross-reporting.

Credit limit improvement

Confirmed and uninvoiced sales orders are included in a partner's total receivables. The credit limit warning considers this new computation.

Deferred expense/revenue report

The Deferred Expense/Revenue report allows auditing any amount. The audit can differ from the reported amount, as those are theoretical computations. Any difference means there is a manual entry to generate.

Deferred management

The management of deferred entries is now distinct from assets. Create deferred revenues and expenses without setting up deferred models in advance.

Delivery date

The delivery date is now a standard field on invoices.

Down payment and POS

The breakdown of taxes and accounts on down payment invoices remains consistent, irrespective of whether the invoice is initiated through the PoS or the Sales App.

Down payments tax breakdown

On down payment invoices, the tax breakdown of the original sale order is now respected.

Early discount

Improved display of due dates for early payment discounts and installments.

EDI format

Added the EDI format and Peppol fields to the customers list view.

Expense receipts in journal entry attachments

Expense receipts are now attached to their respective journal entries. They are also automatically included in the Datev export for the German localization.

Express VAT in local currency on invoices

Tax computation appears in local currency on customer invoices made in foreign currency to comply with the 2010/45/EU directive.

Filter blank lines

Added option to filter out lines at zero from fiscal reports.

Fleet: impact vehicle without a bill

For Fleet and Accounting users, the bank reconciliation widget now allows you to specify the vehicle concerned on any manual operation.

Follow-up reports: missing contact info

When mass processing follow-up reports, contacts with missing information are isolated and do not block the process for other contacts

Import matching number

Matching numbers have been simplified and are displayed with colors. Partial matchings have been improved, specifying which moves are partially matched with a proper identifier. You can now add a matching_number to your CSV import of lines. Odoo waits for all related account moves to be posted and tries to reproduce the reconciliation when that happens. If it fails, the imported reconciliation is discarded.

Improve settings for tax calculation display on invoices

Merged Line subtotals tax display and Rounding Method in Accounting settings.

Improved printed reports

Printed versions of accounting reports have been improved.

Inter-company transactions - Attach a copy of the invoice PDF to the vendor bill

A copy of the invoice is now added to the bill attachment of the counterpart company in the scope of inter-company transactions.

Invoice date visibility

The invoice date was added to journal items, journal entries, and some reports.

Invoice layout overhaul

The invoice layout is clearer. To satisfy the legal requirements of several countries, you can display the total amount in letters.

Invoice upload harmonization

Harmonized invoice uploads in both Accounting and Documents. Draft credit notes can be changed to invoices. Factur-X documents detect the move type (invoice or credit note) from the Factur-X data.

Manual matching: partner creation

Improved partner creation on manual matching by prefilling both the partner name and VAT number.

Manual reconciliation

The manual reconciliation widget was removed. Lines are silently reconciled unless a write-off entry is required, which launches a new reconciliation wizard.

Mass download documents

Use the "send and print" download option to grab all documents from a selection of invoices. The download option on a single invoice now returns a zip file with all documents (PDFs and electronic invoices).

Mass 'send and print': invoice banners

When an invoice is included in an asynchronous mass 'send and print' job, a banner is displayed on the relevant invoices to inform users that the job is in progress.

Matching numbers rework

Matching numbers are color-coded in the Journal Items view. Partial matchings are uniquely identified to see which lines are part of the same partial matching.

Miscellaneous operations on bank journals

Miscellaneous operations involving a bank account are highlighted on the Accounting Dashboard to improve auditability.

OCR usability improvements

Document upload has been optimized: automatic digitization is now synchronous and five times faster. Error messages and warnings were improved.

OCR: credit notes and refunds

Credit notes and refunds are automatically detected by the OCR and created as such.

Payment scamming protection

To prevent users from sending money to potential scammers, vendor bank account numbers must be marked as trusted before they can be used to make an outgoing payment.

Peppol global location identifiers

Add GLN (global location identifiers) to partners' delivery contacts for use with Peppol.

Peppol onboarding

Easily send and receive invoices, bills, and credit notes by registering on the Peppol network.

Ponto onboarding

The Ponto onboarding has been improved.

Report loading speed

Introduced a new prefix group mechanism to improve the loading speed of accounting reports on large databases.

Report sections

Reports can now be grouped on the user interface and in the exports.

Reports rebirth

The interface design of accounting reports has been drastically improved and technically overhauled.

Revamped send and print wizard

Users can select the relevant documents to be generated and the approvals to be requested from the "Send and Print" wizard. The electronic invoicing format is now configured on the customer.

SAF-T: remove blocking errors

Warnings are displayed on the general ledger rather than when clicking the SAF-T export button.

SEPA Direct Debit (pain.008.001.08)

Added support for the pain.008.001.08 format for SEPA Direct Debit.

SEPA non-latin characters

SEPA characters mapping extended to cater to all European languages.

Tax taxonomy

All localizations' taxes now use codes in their names to improve their display and usage in Odoo forms. Tax codes can be searched using shortcuts. The new Tax Description field contains longer descriptions of the taxes.

Taxes: modification restriction and logging

Some fields on the Taxes model are now unmodifiable once the tax is used. Modifications done on some fields are tracked in the chatter.

UBL/CII: handle payment terms

Improved UBL import with Cash Discounts and fixed taxes.

User portal: invoices

Downloading an invoice from the portal downloads all available formats, including electronic invoices.

User-friendly bank synchronization

Bank synchronization flows have been simplified. Buttons and alerts are displayed on the dashboard. Email notifications are sent to the account holder.

Vendor bills import and purchase orders matching

When importing vendor bills from electronic invoicing systems supported by Odoo (e.g., UBL 3.0 invoices), Odoo now does partial purchase order lines matching and bill autocomplete for matching lines based on unit price and product name. The remaining non-matched lines are added separately on the vendor bill.

VIES check

The output of the VIES check is displayed on the partner and can be overridden when necessary. For eCommerce flows, the check can be restrictive, preventing the client from obtaining an invoice on which the reverse charge has been applied.

## Localizations 

Algeria

Accounting: improved the base localization (chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, and tax report).

Argentina

Accounting: added electronic vendor bills and liquidity product direct sales (liquido producto), both with the possibility to create electronic vendor bills and non-electronic sales invoices. Added the check management module to manage your own and third-party checks, and track checks to prevent users from paying with the same third-party check twice. POS: invoice electronically from the POS, in compliance with the AFIP requirements.

Australia

Added the payroll localization: manage compliant contracts, assign different types of salary structures, compute superannuation guarantee and manage super accounts, generate compliant payslips and pay employees using ABA files. Added the link between Australian payroll, accounting and time off. Added ABA payment method for batch payments to banks. Added EFTPOS payment method for PoS via Stripe, and Buy-Now-Pay-Later payment methods via Stripe and Asiapay for eCommerce. Added PEPPOL e-Invoicing format. Added two new reports for partners: customer statements to send monthly to customers, and remittance advice to send to suppliers.

Belgium

Accounting: Download CODA and SODA files directly from Codabox. Added the ability to import CODA files for countries other than Belgium. Implemented National Bank of Belgium's official Balance Sheets and Profit and Loss reports. Payroll: export to SD Worx is now a dedicated module. Display more cost-related information for company cars on the salary configurator. Representation fees are set on the job position.

Brazil

Accounting: added the Central Bank of Brazil as a currency rate provider. Use different document types when creating customer invoices and vendor bills. Add identification types (CPF, CNPJ, etc.) in the same field when creating contacts; alphanumeric CNPJs are supported. Compute Sales Tax on sales orders and invoices using Avatax.

Chile

Accounting: added new fields on the partner form and extra steps in eCommerce to allow users to generate eInvoices and eBoletas based on SII requirements. POS: create electronic invoices and Boletas with all the required legal information thanks to the addition of fiscal information on the contact form, which is editable directly from the POS session. eCommerce: users can create electronic documents from eCommerce, allowing customers to complete their fiscal information and choose the type of document.

Colombia

Accounting: updated financial reports and taxes.

Denmark

Accounting: implemented the SAF-T report and the new chart of accounts, and updated taxes accordingly.

Dominican Republic

Accounting: updated localization, added financial and tax reports.

Ecuador

Accounting: added the balance sheet and profit and loss reports. New withholding taxes are available that are based on the Resolución NRO. NAC-DGERCGC26-00000009.

Estonia

Accounting: added the base localization package: chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, tax report, and IC supply report.

France

Accounting: fiscal roundings added to tax report.

Greece

Accounting: added the base localization package (chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, and tax report).

Hong Kong

Accounting: add FPS QR codes to invoices. Payroll: added the Manulife MPF report, salary rules support (Cap. 57), HSBC AutoPay report, IRD56 (B, E, F, and G) reports.

Ireland

Accounting: added the base localization package (chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, and tax report).

Japan

Accounting: added support for Peppol PINT Japan.

Jordan

Accounting: non-Jordanian customer identification has been added to determine whether the customer is located in or outside Jordan. Improved JoFotara credit note synchronization by refining the line-matching logic. Support has been added for "Transit," "Foreign Trade," and "Free Zone Transfer" invoice types. Point of Sale: improved JoFotara credit note synchronization by refining the line-matching logic.

Kazakhstan

Accounting: added the base localization package (chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, tax report).

Kenya

Accounting: added withholding taxes and default accounts for POS. Configured Inventory valuation. Payroll: added the NHIF and NSSF reports.

Latvia

Accounting: added the base localization package (chart of accounts, taxes, fiscal positions, and VAT report).

Lithuania

Accounting: produce your Standard Accounting Data File (SAF-T).

Malaysia

Accounting: improved chart of accounts and taxes. Added the SST-02 tax report.

Mexico

Accounting: added the Month 13 Trial Balance variant and factura global. Improved the fiscal regime management of customers. Transformed the DIOT report into a tax report variant. Additional required fields for CFDI Invoices are now available on the eCommerce portal during order validation. Added a new step to the eCommerce order flow to let customers supply the required information for e-invoicing. Implemented the four cancellation reasons for CFDI invoices. Added support for Global Invoice sending, generated from the PoS orders or invoices list views. Added standard IEPS taxes. Set Usage and Payment Way fields on sales orders or set default values on partners to improve invoicing automation. Payroll: added monthly pay computation.

Morocco

Accounting: improved the base localization (chart of accounts, taxes, fiscal positions, and financial reports). Payroll: added monthly pay computation.

Mozambique

Accounting: added the base localization (chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, and tax report).

Netherlands

Payroll: added monthly pay computation.

New Zealand

Accounting: added PEPPOL e-Invoicing format for Australia. Added two new reports for partners: customer statements to send monthly to customers, and remittance advice to send to suppliers.

Peru

Accounting: added new reports (balance sheet, profit and loss report, sales book, purchase book, exportation purchase book, general ledger, partner general ledger, chart of accounts - in compliance with SUNAT requirements).

Philippines

Accounting: updated the chart of accounts and taxes. Added the ability to export BIR 2307 reporting entries from vendor bills and payments.

Poland

Accounting: implemented the tax report.

Romania

Accounting: added balance sheet, profit and loss, and SAF-T (D.406 declaration) reports. Implemented eFactura (UBL with CIUS-RO). eTransport declarations based on deliveries have been implemented. The VAT rates and the VAT report have been updated. Support has been added for CPV codes on products in eFactura. Error handling during the ZATCA journal onboarding has been improved.
Payroll: added monthly pay computation.

Serbia

Accounting: translated the chart of accounts and reports. Activated Storno accounting by default.

Singapore

Accounting: added PayNow QR codes to invoices.

Slovakia

Payroll: added monthly pay computation.

SODA file import: account mappings

SODA import has been improved with an account mapping wizard that allows the user to register the mapping between the accounts provided in the SODA file and the accounts of the user's chart of accounts. Non-mapped accounts are created automatically. The mapping is saved for future imports.

Spain

Accounting: added Factura-e invoice generation and signing. Implemented TicketBAI e-invoicing. If given, the company ID will be printed on invoices. This can be used to add a non-VAT customer reference on invoices (e.g., for intracommunity invoices to customers who do not have a VAT number). Export the VAT record books (Libros de IVA) files from the general tax report. Modelo 390 is available in the tax reports. Import Factura-e XML invoices in Odoo. Updated Exento taxes and added reporting compatibility (SII).

Sweden

Accounting: import SIE Audit files.

Switzerland

Payroll: added monthly computation, reports, insurances and canton rules.

SYSCOHADA

Accounting: improved the base localization by revising the chart of accounts and financial reports.

Thailand

Accounting: added PromptPay QR codes to invoices, tax invoice printout, purchase and sales tax report export, and PND3 and PND53 tax reports export as CSV for eFilling purpose.

Tunisia

Accounting: added the base localization (chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, and tax report).

Türkiye

Accounting: Support has been added for additional UNECE codes as Units of Measures for e-Fatura and e-Arşiv.

United Kingdom

Accounting: tax names were updated. The BACS file format is now supported for payments.

United States

Accounting: added support for New York State taxes. Payroll: Added the Payroll localization. Form W-2 is supported.

Venezuela

Accounting: implemented VAT validation on the Venezuelan TIN format.

Vietnam

Accounting: added VietQR code to invoices.

## Appointments 

Book a resource

Manage bookings for resources (tennis courts, restaurant tables, etc.).

Confirmation and cancellation templates

Send specific templates when a booking is confirmed or canceled.

Customer reminders turned off

Operators won't be notified by customer appointment reminders.

Event link option

Use your Google account to send Meet invites to attendees.

Exploration with Gantt view

Manage your bookings from a Gantt view. Add closing days to block reservations.

Invite colleagues to meetings

Allow attendees to invite guests on their bookings.

Link meetings to applicants/opportunities

Meeting invites sent from Leads and Applicants now create meetings linked to those documents.

Meetings linked to applicants / opportunities

Meeting links sent using an existing appointment type are now linked to the applicant or lead.

Multi-website support

Publish appointment types on specific websites.

Non-permanent meeting management

Specify specific start and end dates for appointment types.

Pay to book

Require a payment before a booking is confirmed.

## Approvals 

Approver sequence

When an approvers sequence is set, the manager (if required) will be the first to approve.

Documents integration

Synchronize attachments between Approvals and Documents.

## Attendances 

Full revamp

Full revamp of the app, including the addition of geolocalization, public kiosk mode, and Gantt view.

## Barcode 

Existing quants in inventory adjustments

When a tracked product is scanned in an inventory adjustment, all lots/serials present in this location are loaded.

Manual entry

Added the option to enter a barcode manually.

Manufacturing with Barcode

Process and create manufacturing orders from the barcode app.

Product picture

Display a picture of the product in the details to ensure the correct product is picked.

Returns from barcodes

Create returns in barcode format and conveniently scan barcodes to identify returns. Additionally, the visibility of returns in the portal has been improved, and the return default operation type has been removed for better handling of purchase returns.

Scan source

Users are now asked to scan the source location before the product if it is not supposed to be picked up in the last scanned source location.

Smooth handling of reserved quantities

Better handling of reserved quantities with on-the-fly reservation changes when picking from a different location.

Sound and visual effects

A sound is played when a barcode is not recognized or is wrong. The screen flashes as soon as a barcode is correctly recognized.

## Calendar 

Hide weekends

Calendar views allow users to show or hide weekends. The setting is shared between calendar views.

## CRM 

Meeting date

View the upcoming or last meeting date with a lead directly from the smart button

Tags: leads to activity reports

Tags are propagated from leads to the activity report.

## Dashboards 

Blank dashboards

Create blank dashboards from Dashboards configuration.

Dashboard sharing

Share dashboards from the Dashboards app.

Year-to-date filter

Year-to-date (YTD) can be selected as the filtered period.

## Discuss

Attachments panel

Easily find attachments sent in your Discuss conversations using the new Attachments panel.

Edit sent messages

Edit sent messages in the chatter.

Email addresses in chatter

Display recipients' email addresses when sending a message from the chatter.

Emoji selector: recently used

Find your favorite emojis quickly with the "frequently used" section in the emoji selector.

GIF button

Send GIFs to your colleagues in Discuss.

Mark as unread

Users can now mark messages as unread in their Discuss conversations and channels, to process them later. The sender sees the message as delivered and not seen.

Mentions in the full composer

Mention your colleagues from the chatter's full composer. Mentions are now kept when switching from the small to the full composer.

Notification preferences

Configure your notification preferences for each channel and conversation.

Options menu from username

All Discuss-related options are now available when clicking a username in the chat window header. In addition, open an employee's profile or edit their username in the menu.

Pin messages

Pin messages in your Discuss conversations to quickly access important and old messages. A section with pinned messages is available from the header of your conversations and channels. You can jump to specific pinned messages from there.

Raise hand option from Discuss meeting

Meeting participants can now raise their hands to signal that they wish to speak and notify other participants.

Search messages

Search messages and notes posted on the chatter.

Simultaneous screen and video sharing

Share your screen and video simultaneously in Odoo meetings.

Voice messages

Communicate with colleagues using voice messages.

## Documents 

Trash bin and auto-deletion

Move documents to the trash bin instead of archiving them. They will be deleted after 30 days.

Split tool improvements

The split tool is easier to use thanks to improved ergonomics and shortcuts.

File sharing and downloading

It is now easier to share and download single or multiple files.

Fleet integration

Centralize all Fleet documents in the Documents app.

Multiple pages thumbnail

Easily see if a document contains multiple pages on its thumbnail

Document request

The document request feature was improved with proper activity management and reminder emails.

"All" workspace improvements

Primary buttons and actions can be used from the "All" workspace.

Actions: accounting journal

Select which journal should be used for several accounting-related workflow actions.

Batch upload

Only invalid files are rejected when uploading files in batch.

Bring HR signed documents into Documents

Employees and Payroll signature requests can now be stored in Documents when completed.

Create Expense action

Create an expense automatically based on a file's content with the 'Create an Expense' action.

Drag-and-drop in Workspace panel

Improved the drag-and-drop feature in the Workspace panel.

Improve link management

Improved design of link thumbnails.

Keyboard shortcuts

Added keyboard shortcuts for quick access and navigation.

Navigation through URL

Navigate in Documents workspaces through URLs.

Search by product

Search and group documents by product.

Set contact on emailed files

The sender of a document through email is now set as the contact.

Sharing portal: multiple tabs

Documents are now previewed in a new tab.

Trash Excel files after conversion

When converting a Excel file to a Spreadsheet, users can now choose to keep the file or to trash it.

## eCommerce 

Address detection and management

Customers billing addresses can be detected and easily managed at checkout.

Checkout redesign

Improved the checkout design and flow.

Display product tags

Display product tags on your e-commerce product pages, such as information labels. These tags are also available in filters from the shop page.

Images as attribute values

Use images instead of colors for product variants.

Multi-checkbox attribute

Customers can select more than one option for a product with the multi-checkbox attribute.

Product description layout

Create appealing descriptions of your product using the Odoo text editor.

Remove search bar

Remove the search bar from your /shop page.

Set "Display Product Prices" by website

Configure the "Display Product Prices" setting independently from one website to another and create separate B2C and B2B eCommerce stores.

Shipping methods without inventory

Use shipping methods in your eCommerce without installing the Inventory app.

Show available coupons

Available promotion codes assigned to a customer are now automatically displayed at the last checkout step and can be added with a single click.

## eLearning 

Enrollment and invitation

The enrollment and invitation processes were improved: invite users to enroll or enroll them directly.

Multiple correct answers

Set multiple correct answers in simple quizzes.

Prerequisite courses

Set prerequisite courses.

Resume courses

Attendees can resume their course when rejoining.

Section duration

Attendees can see the duration of each section of a course.

## Email Marketing 

New mailing templates

Five mailing templates have been added.

Unsubscribe flow improvement

Test and customize your Unsubscribe page. Review new optout report.

## Employees 

Add fleet manager to plan

Assign plan activities to the fleet manager of the employee's vehicle.

Certification report

Added a report dedicated to certifications.

Employee widget

All fields referring to an employee display their avatar.

Generate employee CV

Generate a CV in PDF using the information found on the employee record.

Graph and pivot views

Graph and pivot views have been added to the app.

Homeworking

Define a different location for each working day of the week. This schedule can be repeated every week. Users can check in the location of other employees via the Calendar app or the Employee portal.

HR files access link

When archiving employees, send them a link to their private files.

Managers

Managers can view their employees' 'first contract' and 'next appraisal' dates.

Newly Hired filter

Filter your newly hired employees from the Kanban view.

Organizational chart view

A new organizational chart is available on employees and departments.

Presence icon visible

The presence indicator from Attendances is visible even without any rights on the app.

Private car plate

Added a "private car plate" field to search for any vehicle.

Signature requests

Choose to send signature requests to an employee's public or private email address.

Skills

Improved content and usability of the skills library.

## Events 

Attendees list scanning

Use the attendee list's QR codes to register attendees quickly.

Badge dimension and background

Select a badge dimension and customize its background.

Display pricelist picker

Visitors can pick a pricelist when buying tickets.

Event language

Specify the language of an event and automatically translate all communication related to this event into that language.

Filter attendees

Filter attendees based on registration form answers.

Multi-website support

Publish events on a specific website.

Registration form flexibility

Customize questions on the event registration form.

Ticket order

Choose the display order of event tickets.

## Expenses 

Accounting flow revamp

The accounting flow of expenses reports posting has been modified. An expenses report paid by an employee generates a vendor bill, and an expenses report paid by a company generates a payment instead of a purchase receipt. The synchronization between the Accounting and the Expense app has also been improved. The payment method used can now be specified for the expenses paid by the company.

Default category

Specify a default category for automatically generated expenses.

Expense report: payments

Expense reports paid by the company now generate as many payments as there are expenses in order to ease the reconciliation process.

Expense report: improved PDF

The expense report PDF has been improved, and receipts are now attached.

Forced amount in company currency

For expenses made in foreign currencies, employees can manually enter the amount they spent in company currency independently of Odoo exchange rates to match their real spendings perfectly

Improve status consistency

Improved the pipeline stages of expenses and expense reports with consistent terminology. Clarified "to submit" versus "to report" and added tooltips to the expense dashboard.

## Fleet 

Order date

Added an order date field on vehicles.

Search by model or brand

The search for a vehicle now includes the model or the brand.

## Forum 

Front end redesign

New front end design for forums.

Pending posts per forum

Users who don't have their posts automatically validated (insufficient karma points) can now have one pending post per forum.

## Frontdesk 

Visitor management

Keep track of all your visitors painlessly with the Frontdesk app.

## Helpdesk 

Help center quick links

Display links to the most popular knowledge articles, forum posts, and eLearning courses.

## Inventory 

Autobatch

Autobatch only batches operations in the ready stage.

FIFO product costs

The cost of FIFO products is now set to the average price of the remaining quantities.

Flexible reservation

Edit reserved quantities and reserve specific quants.

Forecast report reservation

Reserving and unreserving from the forecast report affects only the selected product instead of the entire operation.

Incoterms

Incoterm and location are now included on the delivery slip.

Lot/serial properties

Lot and serial number properties are displayed on their quants.

Lots expiry and quantity mass entry

Updated mass entry of lots/serial numbers on receipts to allow expiration dates and quantities. This allows users to include these additional fields when pasting multiple lots/serial numbers for efficient data input.

MTO/MTS

When the 'Make to Order' (MTO) process is interrupted, items can be reserved from the available stock to ensure the workflow continues.

New removal strategy: least packages

Added the "Least Packages" force removal strategy that avoids reserving quantities in two packs when the demand is available in one larger pack.

Operations menu

Find the right operation easily with the revamped operations menu.

Packaging display on documents

The packaging is now displayed on purchase orders, sales orders, and transfer documents.

Print at operation type

Print reports automatically at transfer validation. Define which report to print at the level of operation types. If no IoT printer is linked, the report is downloaded instead.

Product quantity update

Update quantity quickly from the product form.

Real-time inventory valuation

Default accounts are now pre-configurable from the Accounting app settings. Introduction of a new 'Cost of Production' account that improves production costing and better differentiates work center and employee costs.

Reception report barcodes

Reception report now includes a barcode for the next step (ex., from pick to pack) to allow quick movements through a workflow with a barcode scanner.

Replenishment improvements

Filter replenishments by vendor and select a list of products to be replenished to their maximum quantity.

Reserve / unreserve button

The forecast report reserve / unreserve button now supports multistep routes.

Revamp pickings

Eliminated the distinction between planned and immediate transfers. A new transfer starts in ready state and can be planned by setting it in draft. Detailed operations of transfer lines allow directly selecting the existing stock.

Shipping-based routing

Specify shipping method on routes.

Stock aging report

Monitor product quantity and time spent in inventory to assess dead stock using the new stock aging report.

## Knowledge 

Article comments

Comment on the content of articles.

Articles-items conversion

Convert articles into items and vice versa.

Collaborate with portal users

Enable collaborative article editing for portal users: they can now edit an article once allowed.

Collaborative mode

Write on knowledge articles alongside other users.

Comments on invites

Add a message when sharing an article.

Drag and drop to delete

Drag and drop an article in the trash.

Embed Gantt views

Embed Gantt views in articles for enhanced project visualization.

Improved /file command

Preview and rename files directly from an article.

Insert banners

Insert banners in Knowledge articles.

Insert videos

Insert videos in your Knowledge articles.

Item calendar

Insert a calendar view of your article items.

Item Kanban view: stages

Create and edit stages in your Knowledge item kanban views

Load templates

Choose from several preconfigured templates to get started quickly.

Manage visibility

Define visibility rules on knowledge articles.

Mobile navigation

On mobile, navigate using a dedicated burger menu icon (yum!).

Portal access

Let your collaborators access their articles through their portal.

Print article as PDF

Print or export your Knowledge articles.

Reposition covers

Reposition article covers.

Save customized groups and filters

Save custom filters and groups on embedded views, and set them as default.

Version history

View the version history of knowledge documents.

## Live Chat 

Additional languages on user profile

Live Chat sessions can be assigned to you according to the languages added to your user profile and the visitors languages.

Improved flows

Find more information on the channel page, add or remove operators, and invite users to a session. Improved the session history page and enhanced the user experience on the front end.

Live Chat calls

Start calls with website visitors from Live Chat sessions.

Message options

Operators and visitors can now edit, delete, pin, and reply to messages as well as use emojis and reactions.

Open session history in Discuss

Open all Live Chat sessions in Discuss as an admin, with limited access depending on your membership (read-only or full edit)

Send attachments

Operators and visitors can now send each other attachments (visitors need the operator's authorization).

## Maintenance 

Maintenance

Redesign of the recurring maintenance creation mechanism. Worksheets are now also available for maintenance requests. Maintenance is now also possible in work centers, with the possibility to block production schedules.

## Manufacturing 

BOM updates

Update a bill of materials and apply changes to existing manufacturing orders. Generate new bills of materials from manufacturing orders. Generate engineering change order requests from manufacturing orders. 

Components demand propagation

Propagate individual manufacturing order component demand changes to pre-production pickings.

Manufacturing orders overview

Visualize all aspects of a manufacturing order from a single report on the MO. Identify missing quantities and trigger respective replenishments or track existing ones. Track the actual MO cost against the predicted cost before, during, and once the production order is complete. 

UX improvements

Manufacturing lead times have been moved to the bill of materials. Check availability from the manufacturing order list view. Expected end date added to MO form view. Print Label added to MO form view. 

Planning

Plan based on component expected arrival dates. Identify manufacturing orders whose replenishments are due to arrive late with the 'Late Components' Filter. Visualize work order dependencies from the 'Planning by Production' view. 

## Marketing Automation 

Campaign templates

Get started quickly in Marketing Automation using pre-made campaign templates.

## Meeting Rooms 

Room booking management

Book office rooms using a tablet-friendly interface.

## Online Payments 

Adyen partial payment capture

If you require customers to pay in advance or leave a guarantee, you can now capture part of the transaction amount and void the rest with the Adyen payment provider.

Currency filter

Filter payment providers based on currency.

Demo provider: express checkout

The Express checkout feature is now supported for the Demo provider.

Demo provider: partial capture

The demo provider now supports the partial capture of payments.

Fees currency

Define payment fees with a currency to guarantee the amount by converting it into the currency of the payment.

Link payment methods with transactions

The payment method used by the customer is now saved on the payment transaction.

Payment forms: payment methods display

Introduced the concept of payment methods in Odoo. Each payment provider is linked to a list of payment methods, which are displayed on payment forms.

Payment link generation

The process of generating a payment link was simplified by using a single button to copy and generate the link.

Public users payment with tokens

Allow users to pay with their saved payment methods without logging in.

Razorpay: tokenization support

Razorpay now supports saving payment methods for future payments.

Remove extra fees

Removed the extra fees feature (Paypal and Alipay).

SEPA: pay once to create mandate

Ask customers to make a first payment to validate the SEPA mandate.

Stripe

Customers can pay directly within the payment form (no redirection to Stripe). 26 new payments methods and Indian e-mandates are supported.

Wire transfers: payment instructions

Add the possibility to regenerate the payment instructions with the available bank accounts for wire transfers.

Xendit

Added the Xendit payment provider covering Indonesia and the Philippines.

## Payroll 

Account number validation

Avoid making payments to erroneous bank account numbers with the introduction of warnings when account numbers are changed.

Automated cancellation of signature requests

Signature requests are now automatically canceled when contracts are canceled or refused.

Bye contract history

The Contract History view was dropped to work with a standard list view, accessible from the employee form.

Conditional advantages

Certain advantages can now be contingent on selecting specific other advantages. For instance, the fuel card benefit is only displayed if the employee opts for a company car.

Existing payslips to open batches

Add existing payslips to an open batch.

Extra hours: work entries

Extra hours are automatically recorded in work entries for employees who work with the Attendance app and with a contract based on a Planning schedule.

Force new car list

The "force new car list" field in the salary configurator wizard is only displayed if the condition set on the "new vehicle request" setting is met.

Fuel type on contract

The fuel type is displayed on employee contracts.

Generic salary attachments

Create salary attachments for multiple employees.

Break periods on working schedule

Work entries are now automatically generated for extra hours, and working schedules can use break periods, which are ineligible for extra hours.

Order salary structure types

Order salary structure types and display them depending on your country.

Payslip viewer

View the PDF of a payslip when it's joined to the record.

Report generation and employee selection

Improved the way reports are generated and how employees are selected.

Salary configurator: Expiration date

Add an expiration date to salary configurator offers.

Salary structure (new/duplicate)

Duplicating a salary structure copies its rules. Creating a new salary structure uses the rules from the archived 'default structure rules set' template.

Worked days and input

Add a "half-day" label in the payslip's working times for better control.

## Planning 

Auto plan open shifts

Automatically assign open shifts to the right employees based on their working hours, roles, availabilities, and time off.

Split shifts from the Gantt view

Effortlessly split long shifts into multiple segments from the Gantt view.

Switching shifts

Request to switch shifts and exchange with your colleagues' unwanted shifts.

## Point of Sale 

Combo meals

Sell food through easy-to-configure combo meals.

Credit limits support

Credit limits set on companies or partners now apply to POS as well. When a limit is reached, a warning is displayed on customer-related views and buttons.

Cross-session floor plans and orders

POS orders are now accessible from other cashier desks. Floor plans can be linked to multiple cashier desks, ongoing orders are shared within the same location, and past orders can be accessed from any desk within a database.

Display rewards and coupons' origin

Cashiers and waiters can now view the origin of rewards and applied coupons, giving them a better understanding of the rewards used for each order.

Floor map in restaurants

The "floors and tables map" setting is enabled by default for bars or restaurants: no need to activate it anymore.

Frontend delivery date

Set the delivery date of any order to ship later from the PoS frontend.

Images on payment methods

Add any image to a payment method.

Non-user employees access

Allow non-user employees to access advanced actions, such as cash in/out.

Preparation display

The easy-to-use preparation screen shows all preparation orders. Cross out dishes or the whole order when they are ready. Get a clear summary of all the food to prepare by product and category.

Preparation tools for everyone

The preparation display and preparation printer are now available outside PoS Restaurant.

Prevent cash payment method re-use

Cash payment methods and cash journals cannot be re-used over multiple POS to avoid accounting issues related to cash drawer control.

Rescue sessions information

Improved information about rescue sessions. Outstanding sessions are highlighted on the dashboard.

Rewards origin and applied coupons

Rewards origin and applied coupons are displayed in the reward popup.

Rewards origin and applied coupons

To give cashiers and waiters a clear picture of what applies to the orders, the reward button now displays the reward's origin and any coupon applied.

Scan a QR code, get the menu

Customers can scan a QR code to get the menu on their device.

Scan and pay

Customers can now pay online by scanning a QR code.

Self-order: online payment

Customers can now pay online when self-ordering.

Self-ordering at table

Your customers can place orders by scanning a QR code at the table and, optionally, pay for their orders online.

Self-service kiosk

Place orders and pay at the self-service kiosk.

Table booking: from Appointment to PoS

Display booked tables on the floor plan in restaurants or bars.

## Project 

Additional task statuses

Mark tasks as done, canceled, in progress, changes requested, or approved.

Create project from SO

Manually create projects from sales orders.

Down payments for project profitability

Get an accurate assessment of your projects' profitability with the inclusion of down payments.

Project access in multi-company environments

Projects and tasks can be made accessible to all companies.

Recurring tasks: generate new on completion

Get ahead of the planning of your recurring tasks: a new task is immediately generated once the previous one is marked as done.

SO generation from projects

Generate sales orders on the fly from projects and tasks.

Sub-tasks and dependency warning on kanban cards

From a task's kanban card, view its sub-tasks and check if it's blocked by another task.

Task configuration shortcuts

When creating a new task from the kanban view, configure it instantly using text shortcuts: set tags, assign users, allocate hours, and set it to high priority directly without opening the form view.

Task scheduling with no allocated time

Schedule tasks with no allocated time in batches from the Gantt view to schedule them one after the other.

## Purchase 

Purchase discounts

Store the gross price and discount in vendor pricelists. You can also modify the discount directly on the purchase order line.

Vendor catalog

Easily add products from your vendor using a kanban view when creating requests for quotation.

## Quality 

Failure locations

Deviate full or partial product quantity to a failure location when a quality check fails.

## Recruitment 

Appointments integration

Schedule a meeting with Appointments and see it on the applicant's profile.

Blacklist email providers

Blacklist email providers to prevent applicants with incorrect contact emails.

Chatter access and offers

Interviewers have access to the chatter. The offer from Payroll is now a dedicated object with its history and security.

Send SMS from list view

Send SMS to multiple applicants from the list view.

## Rental 

Quotation templates, catalogs, and optional products

Quotation templates, product catalogs, and optional products are now also available for rental products.

Single rental period

The rental period of all products on a single order is now the same to simplify the flow. This does not prevent the pickup or return of products at different moments.

Stock Rental

Deliver and return your rental products using multi-steps with delivery and return transfers.

## Repair 

Improved repair process

Manage component reservation and replenishment from the repair module, create repair orders from sale orders and vice versa, and use the 'Recycle' type repair line to easily return previously consumed components into stock.

## Sales 

Add products from catalog

Add products quickly and easily with the catalog view.

Amazon Connector

Manually launch the synchronization of an Amazon order based on its Amazon reference. Shipping confirmations statuses sent to Amazon are now synchronized.

Amazon synchronization

Update the quantities available for 'Fulfilled by Merchant' (FBM) offers on Amazon depending on inventory levels.

Confirmation on down payment

Ask for a down payment from your customers to validate their quotation.

Down payment descriptions

Descriptions of down payment lines on sales orders show the reference and date of the down payment invoice to improve clarity when there are multiple down payments.

Events: support quotation templates

Include event tickets in quotation templates.

Global discounts

Apply a global discount on a sale order.

Loyalty: restricted to pricelist

Define customer-specific loyalty programs based on their pricelist.

Loyalty: starting date

Schedule promotions in advance by adding a starting date on loyalty programs.

No tax on fixed discounts

Taxes are not applied on fixed discounts anymore.

Partial payments

The partial payment flow was improved: it's easier to create payment links and quotations are automatically confirmed when a partial payment completes the total amount.

PDF quote builder

Send attractive quotations to improve your conversion rate. Upload your own PDF files and insert them as header pages, product pages, and footer pages.

PDF reports: lines with zero quantities

Lines containing only zero quantities are no longer displayed in the product variant table on PDF reports.

Pricelist: chatter

Added the chatter on pricelists to ease collaboration.

Product documents

Share documents with your customers automatically when sending a quotation or once the order is confirmed by linking documents to your products.

Quotations mass cancellation

Cancel multiple quotations at once from the list view.

Sales order locked

Lock a sales order at any stage.

Sales without pricelists

Sales flows can now work without using pricelists.

Stat button on customers

Adding a button to access the loyalty cards of a customer from the form view.

## Shipping Connectors 

Sendcloud

Shipping methods now cover a wide weight range and support other shipping options (multicollo, signature request, second delivery attempt, etc.). On eCommerce, use the Sendcloud shipping method with collect points.

Shiprocket

Added the new shipping provider aggregator Shiprocket. This connector allows to send packages with the main carriers active on the Indian market.

Starshipit

Added the new shipping provider Starshipit, covering Australia and New Zealand. It aggregates the most popular carriers in the ANZ region. It supports AustraliaPost, NZPost, Startrack, Direct, etc.

## Shop Floor 

New MRP app

Easily track and complete production orders from the new dedicated Shop Floor application.

## Sign 

Email reminders

Send automatic email reminders to people who have not signed a document yet after a set delay.

Expiration dates

Ensure your limited time offers can only be signed within an allotted window by setting expiration dates on signatures requests.

Mass download of signed documents

Download multiple signed documents in one click.

Signed documents tags

Modify tags on signed documents.

Tag management

Manage tags from the new configuration menu.

## Spreadsheet 

Default number and date format

The default number and date formats are now based on the spreadsheet creator's configuration and can be changed in the file settings.

Multi-cell array functions

Added 30 multi-cell array functions (e.g., UNIQUE, EXPAND, FILTER, TRANSPOSE, SPLIT, etc.).

Dynamic pivot tables

Use the single formula ODOO.PIVOT.TABLE to display an entire and dynamic pivot table.

Smart charts 2.0

Identify tables and automatically set their label/data series accordingly.

Version history

View past versions of a spreadsheet and duplicate it at an earlier stage.

Data validation

Use data validation to control cell entries.

Group rows or columns

Group rows or columns to easily collapse and open them.

Remove duplicate rows

Clean up your spreadsheets with the duplicate rows removal tool.

Split text into columns

Use the split text to columns feature or the SPLIT function to take text from one or more cells and split it into multiple cells.

Trim whitespaces

Clean up your data by trimming spaces and tab spaces.

Share spreadsheets

Share spreadsheets with internal and external users. External users can view, copy the content, and download the spreadsheet. Internal users can also edit it if they have the required access rights.

Dashboard sharing

Share dashboards from the Spreadsheet app.

New functions

Added SORT, RANK, ODOO.PIVOT.TABLE, TRUE/FALSE, HYPERLINK, STEYX, PEARSON, CORREL, RSQ, FORECAST, GROWTH, TREND, SLOPE, LINEST, LOGEST, INTERCEPT, POLYFIT.COEFFS, POLYFIT.FORECAST, SPEARMAN, MATTHEWS, INDEX, UNIQUE, EXPAND, FILTER, TRANSPOSE, MUNIT, RANDARRAY, FLATTEN, FREQUENCY, ARRAY.CONSTRAIN, CHOOSECOLS, CHOOSEROWS, SUMPRODUCT, MINVERSE, MDETERMs, MMULT, SUMX2MY2, SUMX2PY2, SUMXMY2, TOCOL, TOROWS, SPLIT, HSTACK, VSTACK, WRAPCOLS, WRAPROWS, and XLOOKUP

Advanced text formatting

Addition of vertical alignment and text wrapping.

Autofill alphanumeric values

Autofill now supports alphanumeric values.

Autofill beyond viewport

Scroll beyond the visible area of the screen when using the autofill.

Blank spreadsheets auto-cleanup

New spreadsheets left blank and unmodified are now deleted after 24 hours.

Border styles

Use different border styles.

Cell reference when scrolling

Keep track of cells being edited when navigating between sheets.

Chart categories

Aggregate chart categories/labels.

Chart data series: header row

Easily include or exclude a header row from a chart data series

Chart domain edition

Edit the domain of charts inserted from the graph view.

Contextual domain import

The contextual domain is now imported when a model is inserted into a spreadsheet.

Date and time formats

Added more date and time formats.

Drag and drop sheets

Move sheets in the bottom bar by drag-and-dropping them.

Editable font size selector

Type in the desired font size in the font size selector.

Essential keyboard shortcuts

Added plenty of keyboard shortcuts to increase usability.

Extend filters to match new data sources

Field matching is now automatic for data sources already set in the global filters.

Format painter mode

Double-click the format painter to stay in that mode, allowing multiple clicks.

Global filter: "From/To"

Use the "From/To" time range in the global filters.

Global filters order

Reorder your global filters.

Image insertion

Insert images in spreadsheets.

Improve color picker

Addition of color intensity in the color picker.

Improved date parsing

Dates formatted as MM/YYYY are now properly recognized.

Insert functions

Insert and visualize all functions from the Insert menu.

Insert line breaks

Insert line breaks within a cell by pressing Alt+Enter.

Insert links shortcut

Use the Ctrl+K keyboard shortcut to insert a link in a cell.

New functions for prediction assessments

Added functions for prediction assessments.

ODOO formulas insertion

ODOO formulas can be inserted from the function menu.

Search granularity

Find and replace characters more quickly by searching all sheets, the current sheet only, or a specific range of sheets.

Snap to align

Effortlessly align all graphical elements with the snap-to-align feature.

Text vertical alignment on import/export

Vertical alignment is kept when importing/exporting a spreadsheet.

Text wrapping and vertical align

Format your data with text wrapping and vertical alignment.

Text wrapping on import/export

Text wrapping is kept when importing/exporting a spreadsheet.

Use cell as value

Allow dynamic comparisons by setting a cell as the value on conditional formatting.

## Studio 

Approval rules: granular control

Configure multi-level approval flows and notify users when their approval is requested.

Approval rules: server-side enforcement

Approval rules are now enforced server-side (and thus applied for API calls) instead of client-side.

Button visibility

Make any button invisible - permanently or conditionally - in form views.

Currency field configuration

Monetary fields added with Studio now detect and use currency fields on the model by default. Select which currency field to use directly via the Studio view editor.

Custom model publication on website

Create custom website pages for any data in Odoo: integrate Website with any other app, including apps created with Studio.

Disable number formatting

Enable or disable formatting for numbers via Studio (e.g., to display a Year in an integer field without formatting with a thousand separator)

New report editor

Edit PDF reports more intuitively with the new report editor. Use any / command to add static or dynamic content in PDF reports.

Revamped automation rules

Customize your workflows using the revamped automation rules: access your automation directly from any Kanban view, configure powerful automation in a few clicks, send and receive webhooks, and much, much more.

## Subscriptions 

Calendar view

View the next activity scheduled for your customers' subscriptions.

Cancel uninvoiced subscriptions

Cancel confirmed subscriptions when they have yet to be invoiced to avoid considering them as churn subscriptions.

Close reasons: portal retention step

When a customer closes their subscription on their portal, you can add an extra confirmation step to suggest a solution to the customer based on the close reason they selected.

Consolidated invoice

Send a consolidated invoice to customers with multiple subscriptions invoiced on the same day.

Down payment invoices

Create down payment invoices for subscription orders.

First contract date

Added first contract date field on subscriptions to clarify since when contracts are running despite renewals.

Health checks replaced by automated actions

Create automated actions to define the health of subscriptions instead of defining it on each subscription plan.

New MRR reports

Complete rework of subscription reporting. MRR Growth allows seeing the monthly evolution of new contracts, churns, expansions, and contractions. MRR Analysis allows seeing the evolution of your MRR.

Recurring plans

Introduced the concept of Recurring Plans, replacing both the Recurrence and Subscription Plans fields, to simplify the creation of subscriptions.

Renewals and upsells: alternative quotations

Create alternative quotations for renewals and upsells.

Self-service portal

Customers can manage their subscriptions (upsell, renew, close) through the customer portal, increasing their autonomy and reducing the amount of work for salespersons.

Settings and templates

Define a default recurrence on subscription quotations.

Storable products support

Subscriptions can now handle storable products.

Unified subscription status

Manage subscriptions more easily with the single subscription state field. It replaces the various fields used to inform on the status of a subscription. Views and the lifecycle of subscriptions have been adapted accordingly.

## Surveys 

Preconfigured survey types

Surveys now have different types that each come with preconfigurations appropriate for that type of survey.

Triggering answers

Use several answers to trigger a question.

Multiple choice answer display

Improved the answers display for multiple choice questions.

Customizable certificates with Studio

Survey certificate reports are now customizable with Studio.

Duplicate questions

Users can duplicate questions.

Free navigation

Survey takers can navigate freely in a survey even amongst mandatory questions.

New survey notification

Get notified when survey forms are submitted.

Presenter tooltip

Live session presenters see a label on the "next" arrow advising them if the next page will show results, leaderboards, etc.

Questions without correct answers

Survey questions without correct answers are now ignored when displaying the results graph.

Results zoom in

Drill down survey results.

Scoring with answers after each page

Display a scoring with answers after each page.

## Time Off 

Exceed balance

Allow users to exceed their allocated time off balance.

France: part-time employee time off

In France, it is possible to handle time off requests for part-time employees according to the country's specific regulations.

Hourly accruals computation

Use the new "hourly" accruals computation where the quantity is based on each hour spent.

Multiple responsible Time Off officers

Add multiple responsible time off officers to a time off type.

Notification on deletion

Managers are notified when a time off that was previously approved is deleted.

Part time visibility

View partial working schedules in the calendar.

## Timesheets 

Awesome Timesheet deprecation

The Awesome Timesheet app is no longer supported.

Bill timesheets from projects without a sales order

Mark timesheets from a project without a sales order as "billed manually" to get accurate billing rate reports.

Billing rate leaderboard

Boost productivity and competitiveness by displaying a leaderboard based on billing rates.

Grid view: overtime

View overtime or missing hours per day from the timesheets grid view.

## To-do 

Goodbye Notes, Hello To-do!

The Notes app is now To-do, leveraging the task model for seamless project integration and the capability to convert to-dos into actionable tasks.

Command palette integration

Add tasks to your To-do list anytime, anywhere, using the CTRL + K command palette.

## Website 

Background shapes on mobile

Choose if background shapes should be displayed on mobile devices.

Banner building block

Improved the banner building block.

Button labels: edit from panel

Edit button labels easily in the right panel.

Button, video, and image building blocks

Easily add buttons, images and videos with these new building blocks.

Carousel snippet

Reorder your carousel image easily.

Form multiple files upload

Allow visitors to upload multiple files at once when filling website forms.

Grid layout: spacing

Define the spacing between elements in a grid layout.

Grid overlay design

New editor handle and grid design match the new design.

Image hover effects

Add new hover effects to your images to animate your website.

Images drag and drop

Drag-and-dropped images are saved as attachments and can be used after in the editor.

Inner content blocks in grid layout

Drag and drop inner content blocks anywhere in a grid layout.

Instagram feed

Display your Instagram feed on your website.

Navbar customization

Customize the navbar by selecting any text color for menu items.

New color system

Improved the use of theme colors for dynamic pages.

New header templates

Added new header templates, including eCommerce-focused ones featuring a product search bar and a discount banner.

Page templates

Choose from a wide range of layouts when creating a new website page.

Pop-up on click

Display a pop-up when clicking a link.

Responsive user font sizes

Introduced a new font size system to improve website responsiveness and consistency.

Save block with URL image

Images added by URL are saved in Odoo so they don't disappear if the link gets deleted.

Scrap existing website

Convert any existing website into an Odoo website in a few clicks.

Shapes on images

Add new shapes to frame your images.

Specific number of columns on mobile

Choose the number of items per row on mobile devices to increase your website's responsiveness.

Text highlight effects

Add highlights to your website headers, such as hand-drawn circles, waves, etc.

Theme color palette

Customize your website colors using the new theme options.

Top bar access after website creation

You can now access the top bar directly after creating your website; you no longer land on the homepage in edit mode.

WebP image support

Use the WebP image format to make your website faster.

Website configurator: ChatGPT

When creating a new website, ChatGPT generates relevant text for your business.

## Industries 

Industries definition

An Industry module is a suite of applications, configurations, and data that fit a specific business like lawyers, warranties, real estate, etc. It does not contain Python code and can be installed in SaaS databases.

Available online

Community data modules available in apps.odoo.com can be installed on any Odoo platform.

Data modules detection

Modules with only data or configuration (no Python code) are detected on apps.odoo.com to provide it to online customers.

Industries categories

If a data module is detected as one, approved, and categorized as "Industry", it will be available in all databases in the apps menu.

Community modules

Anyone can create a data module and upload it to apps.odoo.com.

Dedicated runbot

A dedicated runbot for Industries has been set up to manage the versioning and testing of modules.

10 data modules released

With Odoo 17, Odoo released 10 data modules: Lawyer, Bar and Lounge, Hairdresser, Certification, Software Reseller, etc.

### Odoo Experience on YouTube

 1. Use the live chat to ask your questions.

 2. The operator answers within a few minutes.

 Watch now
