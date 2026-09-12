# Odoo 16 

## Release Notes
 Try it now

## Accounting 

#### Analytics

Added a new analytic widget on invoices to ease analytic distribution, analytic plans (replacing groups), and analytic distribution models. Break financial reports down. Analyze financial reports according to analytic accounts and/or plans. Mass edition of analytics allowed on List views.

#### Bank Reconciliation Widget

Improved the bank reconciliation widget's navigation, interface, and readability.

#### Usability and Navigation

Updated the navigation throughout the Accounting app to avoid technical views and allow for an easier transition between different screens.

#### Journal Audits

Various Journal Audits improvements: increased overall readability, removed repeated information, empty journals are now ignored, and tax applied details are included.

#### Account Reports

Account Reports were completely reworked to improve their performance, UI, definition, and audit.

#### Accounting Firms Mode

Activate the Accounting Firms mode to access a Quick Total field and manual sequence editing to encode invoices quickly.

#### Accounting Menu

Reduced the number of menu items to help users find the right menu depending on the operation type.

#### Asset Cancellation

It is now possible to cancel an asset.

#### Asset Models

Asset models created from any account automatically use the related account as the default Fixed Asset Account. 

#### Asset Wizards

UI cleaned by grouping several asset modification wizards in one single button.

#### Assets: Negative and Depreciation

Added negative assets management and the possibility to compute depreciations based on the effective number of days in each period. Any asset modification now results in a depreciation entry covering the period since the last depreciation entry was posted. The computation board and assests import were improved.

#### Assets: Non-Deductible Tax

An asset's original value is now increased by the non-deductible portion of the tax (if any).

#### Batch Payments: Chatter and Activities

The chatter and activities scheduling are now available for batch payments.

#### Batch Payments: Rejection Management

Improved reconciliation flow of batch payments containing refused payments: users are now prompted to choose between canceling the original payment or leaving it open.

#### Cash Discounts

Added a new separate definition in the Payment Terms supporting different taxes legislations (included or excluded). Receivable lines are not split anymore, improving the readability of accounting and follow-up reports. The Register Payment wizard suggests using the reduced amount if available. The reconciliation suggests using a computed early payment discount write-off if possible.

#### Credit Limit by Partner

Setup a credit limit per company and/or partner. When the total amount of open invoices for that partner reaches the specified limit, a warning on sale orders or customer invoices is displayed.

#### Currencies

Currencies display reworked on Journal Entries form views and Accounting Journals list views.

#### Currency Conversion Rate: Invoice/Bill Date

The invoice or bill date is now used to set the currency conversion rate instead of the accounting date.

#### Cut-off Entries Label

Improved cut-off entries label to improve ledgers readability.

#### Data Import 

Added a new guide, templates and functionalities to import the following accounting data: contacts, chart of accounts, and journal items.

#### Disallowed Expenses Categories

Assign a Disallowed Expenses Category and Rate to an account from the Disallowed Expenses Categories' list view. 

#### Drag and Drop Upload

Import files by draging and dropping them on the Accounting Dashboard cards (invoices, bills, miscellaneous, bank) or the journals List views.

#### Financial Reports Debug

Use the Finanial Reports' debug tool to analyze which accounts are potentially missing in the report definition.

#### Follow-up Reports

Assign different follow-up contacts to a partner. Exclude a partner from automatic follow-ups. Manually set follow-up levels on a partner. Enhanced wizard to manually create follow-ups. Improved interface: follow-up list, individual report, follow-up levels definition. Filter follow-up reports on follow-up level.

#### Intrastat Commodity Codes Renewal

Intrastat commodity codes have been renewed, and the default transaction code was updated to the two-digit one applicable since January 1st, 2022.

#### Invoices and Bills Payment Widget

The payment information widget on invoices and bills was improved for multi-currencies transactions: the exchange difference entries are highlighted (if any), and, in the info-bullet, amounts are now expressed in both the company currency and the origin currency.

#### Journal Entries and Items Quick Search

Quick searches on journal entries and items were improved.

#### Journal Groups Naming

Journal Groups names are now unique to improve the readability of financial reports.

#### Journals Audit Report

Revamped the Journals Audit report.

#### Lock Dates Tracking

Changes to lock dates are tracked in a chatter on the company's form view.

#### Non Trade Accounts Entries

Journal items on Non Trade accounts are now excluded from Aged reports; a filter is available to add them back in. Entries from Non Trade accounts now appear in the Miscellaneous tab of the Reconciliation widget.

#### Non-continuous Sequence Alert

Smart alerts are displayed on the dashboard when a sales or purchase journal's sequence is not continuous.

#### OCR Background Validation

Uploaded invoices and expenses are validated in the background by the OCR to make the interface more responsive.

#### OCR Field Mapping

The manual field matching interface on invoices was improved.

#### OCR: Bills and Invoices Settings

Activate the digitization of vendor bills and customer invoices separately.

#### OCR: Partner Identification

Enhanced the partner detection on Invoices using bank account numbers and partner auto-complete information.

#### Optional Bank Statements

Bank transactions can now stand alone: bank statements have been made optional.

#### Partners Bank Account Management

Bank account management on partners was improved: all changes are logged in the chatter; a manual validation process was introduced for automatically added accounts before they can be used for outgoing payments.

#### Partners Bank Account Numbers

Assign the same bank account number to different partners.

#### Payment Terms

New payment terms definition screen. Added due date computation methods and dynamic examples.

#### Payments: Total Amounts

Total amounts are displayed when grouping items on the Payments List view.

#### Reconciliation: Foreign Currency

An exchange difference journal entry gets created directly at each partial payment to ensure the rate between the residual ammount currency and the residual amount. When reconciling two lines, one with a foreign currency and one expressed in the company currency, the reconciliation is made using the foreign currency.

#### Recurring Account Moves Management

Recurring account moves for invoices and bills were simplified: an entry can be posted automatically at different frequencies.

#### Reports Readability

Improved the design and readability of accounting reports.

#### Reports: Foldable Records

All tax and financial reports use an improved way of declaring data thanks to foldable parent-children records in editors.

#### Sales and Purchase Journals Alerts

Get an alert on the dashboard when a Sales or Purchase Journal's sequence is not continuous.

#### SEPA Rejection Management

Improved SEPA rejection management: when canceling a payment belonging to a batch, the payment is deleted if no lock date is violated.

#### SEPA: Non-Latin Characters Mapping

SEPA extended to support non-latin characters of all local European languages.

#### Storno Accounting

Use negative debits and credits to reverse original accounting entries by activating the Storno Accounting setting.

#### Vehicle Assets

All depreciation entries can be linked to the vehicle. A "Vehicle split" filter was added to the Disallowed Expenses report.

## Accounting - Localization 

#### Austria

Financial reports are now more robust as they are calculated using Account Code prefixes. Control domains were added where possible using Account Types and Account Tags for debugging and investigation purposes. Din5008 formats are now independent from the German localization.

#### Belgium

Definitive abolition of VAT advance payment: the Grid 91 field was removed from the XML export wizard on the Belgian Tax Report. Tax Names and their search were improved to fasten invoice encoding. Tax Labels were clarified to improve their display on documents. SODA files can now be imported into Miscellaneous Journals.

#### Brazil

The base localization package was added: chart of accounts, taxes, and financial reports.

#### Bulgaria

The base localization package was added: chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, and tax report.

#### Chile

Financial reports are now more robust as they are calculated using Account Code prefixes. Control domains were added where possible using Account Types and Account Tags for debugging and investigation purposes. Added fields  required by the Customs Office on invoices as part of the electronic invoice for the export of goods. Added a demo mode environment to be able to test locally Chilean electronic invoice workflows without the need to have a real certificate and CAFs.

#### Denmark

Financial reports are now more robust as they are calculated using Account Code prefixes. Control domains were added where possible using Account Types and Account Tags for debugging and investigation purposes.

#### Ecuador

The basic package of the localization has been improved: chart of accounts, taxes. Financial reports have been added and adapted: Balance Sheet, Profit and Loss Report, Reports 103 and 104. The electronic documents sending module was added: Sales Invoices, Purchase Settlements, Credit and Debit Notes, Purchase Withholdings, and support for Sales Withholdings.

#### Egypt

Full integration with the Egypt Tax Authority portal. Invoices from companies and invdividuals can be sent to the portal with a single click. 

#### France

As all lines of Tax Settlements are rounded to the nearest euro in France, the Closing Entry is now rounded accordingly (Account Tax Payable / Receivable), taking into account whether it's a Profit or a Loss for the rounding. Float amounts are still kept for a proper reconciliation on the accounts used for the distribution of taxes. New control domains for financial reports were added in debug mode.

#### Germany

Export DATEV with or without PDF files of invoices.

#### Hungary

The localisation was updated: chart of accounts, taxes, fiscal positions, and financial reports.

#### Indonesia

The localisation was updated for chart of accounts and taxes. Minor e-Faktur bugs were fixed.

#### Italy

Added the balance sheet and profit and loss reports.

#### Lithuania

Financial reports are now more robust as they are calculated using Account Code prefixes. Control domains were added where possible using Account Types and Account Tags for debugging and investigation purposes.

#### Luxemburg

New control domains for financial reports were added in debug mode.

#### Mexico

Financial reports are now more robust as they are calculated using Account Code prefixes. Control domains were added where possible using Account Types and Account Tags for debugging and investigation purposes.

#### Pakistan

Added the balance sheet, chart of accounts, profit and loss, and taxes reports.

#### Peru

Added a new field in taxes and adapted the XML to be able to use the ISC tax for Peruvian goods on electronic invoices.

#### Saudi Arabia

Added all value-added taxes, which were mapped with the appropriate accounts on the chart of accounts, and linked to the tax report compliant with the ZATCA format.

#### Serbia

The base localization package was added: chart of accounts, taxes, fiscal positions, balance sheet, profit and loss report, and tax report.

#### Slovenia

The localisation was updated for the chart of accounts and taxes. The balance sheet and profit and loss reports were added.

#### Spain

New control domains for financial reports were added in debug mode.

#### Sweden

Financial reports for K1, K2, and K3 accounting were added. Control domains for financial reports were added in debug mode.

#### Switzerland

Din5008 formats are now independent from the German localization. Print QR-invoices in batch.

## Appraisals 

#### 360 Feedback Template

Improved the default survey template for 360 feedback.

#### Appraisal Date

The default Appraisal Date is now based on the employee's contract start date. If none exists, the employee's creation date is used. Edit the appraisal date of multiple records at once.

#### Employee Skills

Create new employee skills directly from an appraisal.

#### Reporting: Skills Evolution

Added a report on the evolution of employees' skills

## Approvals 

#### Approvers

Set approvers in sequential order.

#### Inherited Rights

Users can see the requests they have to approve without specific access rights.

## Attendances 

#### Extra Hours

When checking out, see the extra hours for the day and the total of extra hours only if the number is positive.

#### Kiosk Mode Authentication

Allow users to choose their authentication method in kiosk mode. Define the duration of the welcome message and which camera will be used for the barcode scan.

## Barcode 

#### Operations

The operation Form view was optimized for mobile devices and the chatter was added.

#### Packagings: Stock Moves

Set packagings during stock moves with the '+package' button for even faster operations.

#### Pickings Deletion

Delete a product that was scanned but not part of an order.

#### Receipts

View supplier reference on receipts.

#### Scanning Packages

Filter transfers by scanning packages. Open a transfer by scanning a package or a transfer barcode on the list of transfers. Scan a package to know its content from the main menu.

#### Scanning per Operation Type

Define per operation type what needs to be scanned or not: source location, product, serials, package, destination location. Better instruction in the top bar. 

#### Serials and Lots

Serials and lots are now folded by default. Scrolling them when unfolded was improved. View the expiration date.

#### Transfers: Chatter and Kanban

Added the chatter to transfer information and a Kanban view of the moves.

#### User Assignation on Picking

Avoid having two persons doing the same operation by automatically assigning a user on a picking/batches as soon as this user changes quantities.

## Calendar 

#### Readability

Improved calendar colors to increase readability.

#### Video Calls

Create a video call from the Event form. Join the video call from the Event form or the meeting popover.

## Consolidation 

#### Profit and Loss

Consolidated Profit and Loss can now display positive profits and negative losses (instead of debit minus credit) when using the 'Invert Balance Sign' toggle button.

## CRM 

#### Archiving Partner Activation Levels

Archive partner activations levels.

#### Languages Installation

Install new languages from the List view.

#### Lead Analysis

View all leads you have assigned and how they are doing from a single reporting place.

#### Lead Language

If a lead has no language set, it now gets the one set on the customer linked to it.

#### Link Opportunity

When creating a calendar invite from a lead, the resulting event is now linked to the lead.

#### Lost Reason

Add a closing comment when an opportunity is marked as lost.

#### Merging Leads

When merging leads, reports are now folded on the chatter and regroup all the information from the merged leads.

#### Merging Leads

When merging leads, all empty fields of the destination lead are completed by the source leads.

#### Recurring Revenue

Recurring revenue totals are displayed in the Kanban view's column headers.

#### Reseller Tracking

Track opportunities resellers (partners) are handling from their stat button and on the Kanban view.

#### Similar Lead Detection

Detect similar leads based on their phone numbers.

## Discuss 

#### Conversations Dropdown Readability

Improved the conversations dropdown menu (inbox) readability.

#### Copy Invitation Link

Added a copy button to channel invitation links.

#### Download Images Button

Added a Download button when hovering image attachment in Discuss or on the chatter

#### Emojis

Added a wide selection of emojis structured in categories and a search functionality.

#### Join/Leave Notifications

Join/Leave notifications in channels were redesigned to be less obtrusive.

#### Link Previews

Link previews are displayed when a message is posted.

#### Message Actions List

The message actions list has been redesigned with a compact/expand mode.

#### Notifications

Notifications are displayed for all tracked changes (e.g., stage changed, task ready) in the systray.

#### Open DM Chat on Mention/Member Name

Open a DM chat with a user when clicking on the @mention, both on a DM chat/chatter, or a user's member name in Discuss.

#### Search Current User

The current user is now included in the chatbox search results.

#### Show Guests

Guests are now shown in member lists.

#### Video Background Blur

Blur your background during video calls.

#### Video Calls UI

The video calls UI was improved, with a dedicated side panel for settings.

## Documents 

#### Disabled Buttons

Buttons are now disabled on read-only workspaces.

#### List View: Group By

Use the Group By option in List view.

#### Maximum File Size

Set a maximum size for files. A clear error message is now displayed if the file is too large.

#### Navigation Panel

Resize the navigation panel to increase readability. The Settings icon appears on mouseover. 

#### OCR Search

Search the content of a document that went through OCR.

#### Sharing

The process to share documents is now faster. 

#### Split Tool: Cancel and Archive

Added the Cancel and Archive buttons to the Split tool and various UI improvements.

#### Thumbnails and Previews

Thumbnails appear for more file types. Previewing documents was improved.

## eCommerce 

#### Abandoned Carts Reminders

Send abandoned cart reminders automatically to customers.

#### Alternative Products Building Block

Customize the 'Alternative Products' block at the bottom of the product page.

#### Archived Product Variants

Archived product variants are not selectable in eCommerce anymore.

#### Attribute Display Type

Change product attributes display types (button, radio, color, etc.) from the front-end.

#### Back in Stock Notification

Customers can be notified when a product is back in stock. They can sign-up directly on the product page, even if not signed in.

#### Cart Items Number

The number of items in the cart is now constantly up-to-date.

#### Coupons, Loyalty Cards and eWallets

Offer promotions, coupons, loyalty, and gift cards to customers. Track their usage across the Point of Sale, Sales, and eCommerce apps.

#### Customer Address Autocomplete and Validation

If using Google Places API: provide address autocomplete and validation during the eCommerce checkout.

#### Mandatory Sign-in

Improved the checkout process when customer sign-in is mandatory.

#### Mondial Relay Delivery

Allow customers to choose a Mondial Relay pick-up point during checkout.

#### Page Options

Change page options in edit mode from the right panel.

#### Picking and Pay on Site

Added the option to pick up and pay in-store during checkout.

#### Prevent Sale of Products

Prevent visitors from adding some products to their carts. For example, B2B companies might not want to display prices for not connected users or not at all if the shop should just be a catalog.

#### Product Pictures

Upload multiple pictures of a product in bulk. Display product pictures in a grid or caroussel.

#### Product Snippet

The Product Snippet was redesigned.

#### Product Tags

Facilitate product filtering in the backend and frontend using product tags.

#### Products Default Sorting

Set the default way products are sorted on the Shop page.

#### Re-order from Portal

Allow customers to re-order the same products from their portal.

#### Rental

New integration between Rental and eCommerce: customers can now order rental products directly from the website. Customers can search for rental products that are available for a specific period. The search can be done using a specific bloc added somewhere on a website or directly in the product catalog with a date filter.

#### Shop Page

New default design for the Shop page

#### Sidebar Filters

The eCommerce sidebar has been redesigned. They appear on the right panel on mobile. Filter categories using the new filmstrip bar.

#### Snippet: Add product to Cart

Create buttons that will add a product to the customer cart.

## eLearning 

#### Banner Header

Customize the banner header of courses.

#### Bought Courses

Improved tracking of bought courses for customers. Added a new 'course' product type.

#### Course Duplication

When duplicating a course, all its content is now also copied.

#### Done/Undone Content

Attendees can mark content as done or undone.

#### Onboarding

A shortcut allows attendees to jump into the courses they just bought.

#### Publication Tracking

Track on the chatter when content is published or unpublished.

#### Registration Prompt

Visitors are prompted to register for the course when they are shared a course they cannot access.

#### Test History

Attendees can overview all their certifications attempts.

#### Vimeo

Use Vimeo links for video lessons.

## Email Marketing 

#### Contacts Mass Edit

Mass edit mailing contacts from the List view.

#### Image Shapes

Use shapes to transform mailing images.

#### List View

Sent and scheduled mailings appear in chronological order on the List view.

#### Mailing Contacts Import

Import contacts in a mailing list by pasting their email address. Download an import template for mailing contacts to quickly see how it must be formatted.

#### Mailing Reports

Turn off 24h stat mailing reports in the settings.

#### Newsletter Snippets

Customize the website Newsletter snippets and create custom forms.

#### Save Filters

Save marketing filters used for audience segmentation and reuse them for future mailings.

#### Template Management

Save mailings as templates to reuse their design later.

## Employees 

#### Departments

New smart button on the Departments' form view displaying the number of employees.

#### eLearning Smart Button

Added a smart button with a link to the user's eLearning profile.

#### Employee Tags

Users with Employee Officer rights can now edit employee tags.

#### Filters and Search

Added 'My Team' and 'My Department' filters to the Employees view and a search option for 'Managers'.

#### Fleet

The 'claim car' report is replaced by the 'cars' smart button, which now displays the vehicles' history of an employee.

#### Mexico

Localization of the Employees app for Mexico with additional fields on the employee record.

#### Multiple Plans

Start plans for multiple employees at once.

#### New User: Employee Creation

Added an option to create an employee directly when using the 'new user creation wizard'. Added a 'create user' action when viewing an employee profile.

#### Plans by Department

On/Offboarding plans can now be linked to a department.

#### Plans: Request Signature

The 'Request Signature' activity now automatically sends a signature request by email.

#### Signature Request Wizard

Added the signature request wizard to the app.

#### Skills Report

Access a report displaying all skills of employees.

#### Skills Wizard

The design of the skills creation wizard has been reworked. A default skill level has been added.

## Events 

#### Locations Order

Choose the order event locations.

#### Mandatory Questions

Define mandatory questions on events.

#### Registration Statistics

Track event registrations with a dedicated stat button.

#### Revenue Reporting

Overview event revenues in a dedicated reporting menu.

#### Tag Order

Order event tags as you want on your website.

#### Website Theme

Event pages are now adapted based on the website theme.

#### Website: Timezones

Timezones are now displayed next to all times on all pages of an event to avoid any confusion.

## Expenses 

#### Default Journals

Configure the journals used by default when creating expenses.

#### Link to Sales Orders

Access Sales Orders impacted by expenses directly from the Expenses Report.

#### Re-invoicing Expenses

Expenses that are re-invoiced on a sales order now impact the sales order's total cost and will not change the margin.

#### Taxes Compatibility

All taxes configurations (included in price and excluded from price) are now compatible with Expenses.

#### UX Improvements

Default expense categories have been added for fresh installations of the app. Menus and filters have been simplified for Expenses Officers. Create expenses en masse by dragging and dropping documents in the app.

## Field Service 

#### Open Reporting

All Field Service users can access the reporting menu and view the available information to their access rights level.

## Fleet 

#### Chatter Tracking

CO2 Emissions, Recurring Cost, and Activation Cost are now tracked in the chatter.

#### License Plate

New 'Cancellation Date' field for license plates.

#### UX Improvements

Various changes to improve the UX.

## Helpdesk 

#### Help Center and Knowledge Integration

Redesigned the Help Center to encourage customers to check eLearning courses, forum posts and knowledge articles before submitting a ticket.

#### Open Reporting

All Helpdesk users can access the reporting menu and view the available information to their access rights level.

#### SMS Templates on Stages

Send an SMS automatically when a ticket reaches a particular stage.

#### Ticket Assignment: Time Off

When tickets are automatically assigned, employees on time off are skipped.

#### Tickets Analysis

Get statistics on the average time taken to answer tickets and first respond to a ticket.

#### Tickets Sequence

Customize the sequence (ID number and format) of your tickets.

#### Tickets to Tasks Conversion

Convert tickets to tasks and vice versa.

#### UTM Parameters

When a lead is converted into a ticket, the values of the lead's UTM parameters are propagated to the ticket.

## Inventory 

#### Automatic Batch Transfers

At the level of Operation Types, decide how to automate the creation of batches, either per contact, carrier, or destination country.

#### Force Backorders

Decide if backorders are automatically created or not per operation type.

#### Inventory Adjustements

Added storage categories and last count date. A warning icon is now displayed next to duplicate serials. Added an 'Apply all' button at the top of the screen and a filter for starred products.

#### Lots and Packages: GS1-128 Barcodes

It is now possible to create GS1 labels for lots and serial numbers (containing product, lot/SN, expiration date, and sell to date). Printing Package content also prints datamatrix codes for all content (including lot/SN, expiry dates, etc.).

#### Reception Report

Improved reception report with the addition of links to Sales Orders, product names on labels, automatic display on Barcode, and moving the auto-popup setting to the operation type.

#### Replenishment Locations

Automate replenishment on specific locations by setting them as Replenishment Locations. Use the left panel on replenishment to sort by location and/or product category.

#### Replenishment: Warehouse Levels

When resupplying from other warehouses, view their available stock from the replenishment report vendor info.

#### Reporting and Menus

Revamped all reporting. Move easily from one report to another. Reworked menus.

#### Transfers: Done Quantity

Easily edit the quantity done in transfer operations.

#### Visibility Days

Set visibility days on a replenishment rule to consider the quantity needed after the forecast date.

## Knowledge 

#### Manage Knowledge

Manage your company knowledge with a dedicated app.

#### Copying and Duplicating

Create a copy of an article or duplicate its whole hierarchy.

#### Cover Picker

Pick Unsplash images as article covers.

#### Embedded Views and Owlification

Use the /item command to create custom databases inside articles.

#### Nested Articles

Nest article items that share the same property fields inside articles.

#### Outline and Index

Insert indexes and outlines in articles to showcase nested articles.

#### Table of Contents

Add a clickable table of content inside articles.

#### Trash Management

Deleted articles are granted a grace period before being removed permanently.

## Live Chat 

#### Chatbot

Use Chatbots to welcome and guide website visitors.

#### Live Chat Button

Display a notification alongside the Live Chat button.

## Lunch 

#### Delivery Notification

New 'Confirm Reception' button to send a notification to employees when their lunch has been delivered.

#### Order Sent Stage

Added a new stage, 'Order sent'.

## Manufacturing 

#### Allocation Reports

Allocation Reports are now also available for manufacturing orders. If two manufacturing orders are linked, parent/child relationships are created automatically. If a manufacturing order is linked to a sales order, its reference and status can now be seen on the customer portal. 

#### Continuous Production

For Manufacturing Operation Types, set up the automatic consumption of lot/serial number tracked products during production. Automatic propagation of quantity changes on the manufacturing order, to both pre and post production transfers.

#### Expired Lots Notifications

Receive notifications when using expired lots for both manufacturing orders and pickings operations.

#### Kit Valuation

Set a cost share on Kit type BOM components to automatically split purchase orders prices on component valuation.

#### Manual MO Consumption

Select components on the Bill of Materials that should be registered manually during production. The consumption of these components then has to be recorded manually before closing the manufacturing order.

#### MES

Complete redesign of the work order Tablet view. Four different types of BOM Improvement Feedbacks are now available from the Action menu. Employees can log in to work centers and work orders, individually or simultaneously. Their timesheet cost is then also considered in the computation of the final manufactured product cost.

#### MO/BOM Overview

Complete redesign of the Structure and Cost report renamed Overview. Lead Times and Next Availability Date, based on current and forecasted stock, are now available.

#### MPS

Import and export products and their forecasts. Delete and replenish in batches.

#### Operation Dependencies

Create operation dependencies on the BOM that dynamically influence both the planning and status of work orders.

#### Products and Forecasts Import/Export

Easily import/export products and forecasts.

#### Return and Repair

Create a repair order from a return. Once the return has been processed, the respective repair order is notified.

#### Split and Merge MOs

Split manufacturing orders for batch production or splitting and assigning production to different employees or work centers. Merge manufacturing orders for batch production or planning purposes.

#### Subcontracting Portal

Manufacturing subcontractors can now register their production from their customer portal. Specific access rights can be granted so that only relevant production documents are shared on the portal. 

#### Usability

Identify unbuilt manufacturing orders with a default filter on the list view.

#### Work Centers Capacities

Expanded Work Centers' specific capacities by considering both Set Up and Clean Up times. 

#### Work Orders: Product Search

Search Work Orders by their final product to produce.

## Online Appointments 

#### Auto-publish and Redirection

Appointment types created from the website are automatically published. When there is only a single type of appointment available, visitors are redirected to its calendar.

#### Booking Calendar

The booking calendar now opens by default the month with the first availability, uses the visitor's timezone if no location is set on the appointment type, and uses the visitor's browser language to format the week and time.

#### Default Reminders and Schedules

Appointment types created on the website now work out-of-the-box thanks to default reminders and schedules.

#### Design and Flexibility Adjustments

Shared links now keep set filters through the entire booking flow, plus various small-scale design improvements.

#### Front-End Creation

Select users when creating a new appointment type from the front-end. Added tips to assist the configuration of new appointment types from the front-end.

#### Link Types

When sharing a link to an appointment type, choose which user(s) can be selected: you, all assigned users, or specific users.

#### Location

Locations of physical events are now using full addresses from the Contacts form.

#### Meeting Links

When an appointment is online (no location), a link to an online meeting is added to the appointment confirmation.

#### Midnight

Appointment slots can go up to 00:00 (midnight).

#### Onboarding

Schedule appointments quickly in three steps with the Onboarding Panel.

#### Operator Selection

Let customers pick their operators.

#### Private Access

Create custom appointment links and configure the URL displayed. Share appointment types that are not published on your website with specific attendees.

#### Questions Reporting

Overview answers provided by attendees while they booked meetings.

#### Users and Time Slots

Assign users to specific time slots; they will then be the only users available at these times.

#### Visitor and Lead Link

When booking an appointment, the visitor is linked to the generated lead.

#### Visitor Tips

Added tips for visitors when no appointment slots appear.

#### Work Hours Restrictions

Bypass work hours restrictions and book meetings at any time made available on the appointment type.

## Online Payments 

#### Adyen: Manual Capture

Added the option to enable two-step payments for Adyen by first booking the amount on the card, then manually capturing the amount (e.g., for hotel bookings).

#### Alipay/Ogone/PayUmoney/PayU Latam Deprecated

Alipay, Ogone, PayUmoney, and PayU Latam are deprecated. They will be removed in the future.

#### Amazon Payment Services

Amazon Payment Services is available as an online payment provider for the Middle East.

#### AsiaPay

AsiaPay is available as an online payment provider for several APAC countries.

#### Authorize.net: Refunds

Online payments made with Authorize.net can be refunded from Odoo or the Authorize.net dashboard.

#### Customer Portal

Payment methods (tokens) of disabled providers are hidden from the customer portal.

#### Demo Provider

The 'Demo' payment provider now supports all additional payment features: saving cards for later, manual capture, customer fees, and refunds. Users can also choose the outcome of their payment to test different payment flows.

#### Express Checkout

Enable quick checkout for Google Pay and Apple Pay users with Stripe: the billing and shipping address are populated automatically.

#### Filter Providers

Filter which providers are shown on payment forms based on the maximum payment amount.

#### Flutterwave

Flutterwave is available as an online payment provider for Africa.

#### Invoicing/Accounting

Online payment providers can now be used without installing the Invoicing/Accounting apps.

#### Mercado Pago

Mercado Pago is available as an online payment provider for Latin America.

#### Payment Tokens Display

The payment tokens display has been adapted to fit on any screen.

#### Razorpay

Razorpay is available as an online payment provider for India.

#### SEPA: Instant Confirmation

SEPA Direct Debit payments are immediately confirmed when the mandate is created to streamline the payment flow and allow for better handling of cancellations and failed payments.

#### Stripe: Manual Capture

Added the option to enable two-step payments for Stripe by first booking the amount on the card, then manually capturing the amount (e.g., for hotel bookings).

#### Stripe: Refunds

Online payments made with Stripe can be refunded from Odoo or the Stripe dashboard.

#### Stripe: Country Restriction

Prevent users from setting up Stripe when they are not in a country where Stripe is supported.

#### Unpublish Providers

Unpublish online payment providers to hide them from customers.

## Payroll 

#### Belgium

On a Recruitment job position, a Payroll tab was added to define if the job is eligible for 'Intellectual Property' and 'Withholding Taxes Exemption'. Related warnings were added to the Payroll dashboard.

#### Dashboard

Created a dashboard to provide an overview for payroll officers.

#### Document Folder for Ex-employees

Send ex-employees a link to access their documents.

#### Entries from Planning and Attendances

Contracts can have work entries based on Planning, Attendances or, a fixed schedule.

#### Kenya

Added a localization module for Kenya.

#### Luxembourg

Added a new module, 'Luxembourg Localisation', with the salary structure and dedicated rules.

#### Manager Access Rights

Added the Manager access rights level, allowing them to read their team members' contracts.

#### Payslip PDF Name

The month is now included in the PDF file name generated for the payslip.

#### Salary Configurator

Drop-down lists are now keyboard responsive: jump to the selection corresponding to the keys pressed.

#### Sign

When requesting a signature, the name of the document in Sign is based on the name of the signed file.

#### Tour

Added a tour to ease the onboarding.

## Planning 

#### Flexible Hours Management

Manage the planning of employees with flexible hours.

#### Material Resources Role

Define a role for material resources.

#### Open Reporting

All Planning users can access the reporting menu and view the available information to their access rights level.

#### Print

Print the planning of employees.

## PLM 

#### BOM ECOs Creation

Added a new and faster way to create BOM-type ECOs. 

## Point of Sale 

#### Coupons and Loyalty Cards

Offer promotions, coupons, loyalty, and gift cards to customers. Track their usage across the Point of Sale, Sales, and eCommerce apps.

#### eWallets

Introducing to Point of Sale the new multi-channel wallet to allow paying goods or collecting refunds.

#### France: Price Restriction

Relaxing restriction against price modification for France.

#### General Settings

Manage PoS configs from the general settings. Switch between PoS configs like between Websites. Search easily over cleaned up and re-organised settings.

#### Global Barcode Nomenclature

The barcode nomenclature is now global and applies to all PoS.

#### Invoices: Closed Session and Client Portal Request

Generate invoices for sales orders of previously closed sessions. Customers can now require invoices themselves via the portal, with the introduction of an option to add QR codes on Receipts.

#### Loyalty Model

PoS is now aligned with the multi-channel model to manage loyalty programs across the Sales, Website, and PoS apps.

#### Margins and Costs Visibility

Hide margins and costs information in the Point of Sale interface for all users but the PoS manager.

#### Order Auto-confirmation

Auto-confirm PoS orders after an electronic payment when nothing is left to pay 

#### Quotations/Orders Total

Actual totals on quotations/orders are now displayed, letting you know what's left to pay before loading the quotation/order.

#### Simplified Interface

Many usability improvements over all screens of the Point of Sale to ease usage.

## Project 

#### Documents Management

Manage documents directly from tasks with the 'Documents' smart button.

#### Documents Workspace

Get the right workspace for project documents by defining a workspace template on service products.

#### Gantt View

View the current allocation of resources with the progress bar in Gantt view.

#### Milestones on Tasks

Link milestones to tasks and mark milestones as reached when tasks are done.

#### Open Reporting

All Project users can access the reporting menu and view the available information to their access rights level.

#### Recurring Tasks Planning

Save time planning recurring tasks with the automatic calculation of their planned date.

#### Reporting: Project Updates

The Project Updates' side panel was revamped to better track the profitability of projects, providing an overview of costs, revenues, margin, and budget.

#### Services Invoicing Policies: Milestones and Manual

Deliver services automatically by linking them to project milestones with the 'based on milestones' invoicing policy. Deliver them manually with the 'based on delivered quantity (manual)' invoicing policy.

#### Smart Task Scheduling

Schedule tasks in batch from the Gantt view without getting any conflicts: the tasks' remaining hours, the user's time off, and their workload are automatically taken into account to find the best possible schedule.

#### SMS Templates on Stages

Send an SMS automatically when a task reaches a particular stage.

#### Tags Management

Get relevant tags for project tasks: only the tags already present in a project are displayed in the tags auto-complete.

## Purchase 

#### Bill Price Matching

The cost is corrected when the bill price is different than the purchase order price.

#### Call for Tenders

Revamped call for tenders. Create alternative requests for quotations and compare them easily, globally or line per line. Creating a dedicated purchase agreement is not needed anymore.

#### Purchase History

View the purchase history of a product on RfQs, helping to track its price.

#### Receipt Status

Receipt status added on purchase orders. This status allows viewing at a glance if the products have been received, partially received, or not received. It also allows seeing if they were received on time.

## Quality 

#### Operations: Quality Checks

Create quality checks on operations.

## Recruitment 

#### Skills on Applicant

Set skills on an applicant; they will be transferred to the employee if created.

#### Applicant Duplicates

When adding or editing an applicant form's email address, searching for an existing address is now case-insensitive.

#### CV Digitization

CVs can be digitized to automatically extract the following information: name, email, and phone. Access CV previews directly in the app.

#### Improved Views

Revamped the dashboard to make them more intuitive, plus several improvements to various views.

#### Interviewer Access

Add users as 'Interviewers' to a job description or an applicant record so they can access the applicant record(s) without having full rights on the Recruitment app.

#### Job Application

Improved the UX and possibilities to apply to a job. Added new fields and new visuals to the job application page.

#### New Reports

Added two new reports: 'Source Analysis' and 'Time In Stage Analysis'.

#### Signature Requests

Send documents to sign to applicants.

#### Team Performance Report

Added a new report about team performance.

## Referrals 

#### Background Image

Change the Referrals background image in the app settings.

## Rental 

#### Schedule: Quantities

See the owned quantity of each storable product in Schedule view.

## Sales 

#### Amazon Account Onboarding

Linking an Amazon account with Seller Central takes only a few seconds now.

#### Amazon Marketplaces

The Amazon Connector now supports all Marketplaces, including brand new ones.

#### Canceled SO Confirmation

When canceling a sales order already sent to a customer, the user has to confirm the cancellation. There is also an option to email the customer.

#### Canceled SO/Invoice Payment

Prevent users from paying for a canceled sales order or invoice through a payment link.

#### Coupons, Loyalty Cards and eWallets

Offer promotions, coupons, loyalty, and gift cards to customers. Track their usage across the Point of Sale, Sales, and eCommerce apps.

#### Delivery Status

Delivery status added on sales orders. This status allows viewing at a glance if the products have been delivered, partially delivered, or not delivered. It also allows seeing if they were delivered on time.

#### Down Payments Readability

Improved the UX when a down payment invoice is linked to a sales order. 

#### Partner Pricelists

When changing a partner's pricelist, a warning is displayed if there are open Sales Orders, including the Website shopping cart.

## Shipping Connectors 

#### DHL and UPS: Commercial Invoices

Added support for commercial invoices for DHL and UPS connectors. Added country of origin on the product form (required for commercial invoices).

#### FedEx Collect Points

Choose a FedEx collect point in eCommerce

#### Insurance: UPS, FedEx, DHL and EasyPost

Insure parcels sent by UPS, DHL, FedEx and EasyPost. Define the percentage of the total value of the shipment that should be insured.

#### Sendcloud

New shipping connector with Sendcloud. Sendcloud is an aggregator of the most popular western Europe carriers. It supports, among others, DPD, Mondial Relay, GLS, PostNL, etc.

#### Shipping Methods

In shipping methods, the postcode range was replaced by a selection of multiple postcode prefixes. The option to 'Pick up in store' was added. Additional description field for the shipping method is now available.

#### Weight Limit

Added weight limit to limit the choice of shipping methods available, depending on the total weight.

## Sign 

#### Auto by Default

Auto signature is suggested by default when the user has never provided a signature (instead of Draw).

#### Extra-Authentication Credits

Allow signatories to submit their signature even if IAP credits (e.g. for SMS authentication) are depleted and warn users regarding low or missing credits via email and on the Sign app interface.

#### itsme Integration

Allow signatories to provide their identity using the itsme identity service (Belgium and Netherlands only).

#### Mobile Layout

Improved signing experience on mobile devices.

#### Multi-company

Improved support for multi-company environments: all outgoing signature requests now come from the right company, even if the sending user changes company.

#### Reassign Signatory

Change the signatory of a document after it was sent.

#### Refusal

Allow recipients to refuse to sign a document.

#### Reply-to Signature Request

Email replies to signature requests are routed to the requester's email.

#### Reporting

New report computing ecologic savings made (paper, water, wood, carbon, waste, energy) using Sign instead of wet signatures.

#### Share Document

Improved document sharing behavior.

#### Signing Order

Send signatures requests in a specific order, ensuring signatories only get notified when it is their turn to sign the document.

#### Signature Frame

A visual frame is added to signatures, ensuring paper copies contain a visual clue that the document was signed electronically.

#### UX Improvements

Access documents waiting for your signature and documents you sent out more easily with new menu entries.

## Social Marketing 

#### Account Owners

See who linked accounts and created streams.

#### Leads

Create leads from any social post or comment.

#### Twitter

Retweet and quote tweets from the dashboard. See how many characters are remaining when writing a Tweet.

## Spreadsheet 

#### Open Source

The Spreadsheet library has been open sourced in LGPL.

#### XLSX Files Conversion

Convert an uploaded XLSX file to Odoo Spreadsheet for edition.

#### Freeze Panes

Freeze rows and columns for better readability.

#### Records on List()

See records on list() function.

#### Absolute Cell References Keyboard Shortcut

Update absolute cell references by using the F4 keyboard shortcut.

#### Accounting Functions

Added a new set of functions to get accounting measures (e.g., balance, credit, and debit).

#### Auto-expanding Leaf Groupbys Removal

The Leaf Expanding Groupbys were removed.

#### Automatic Currency Formatting

The currency formatting is automatically applied to monetary values on pivots and lists.

#### Charts

A chart's default font color is now based on the chart's background color to maximize readability. Added global filtering for charts. Empty rows are now skipped when plotting charts.

#### Charts/Figures Context Menu

Right-clicking on a chart/figure opens the context menu.

#### Clear Formatting

Added 'Clear formatting' option on the Format menu.

#### Color Picker

Improved the color picker's default colors and added the possibility to create custom ones.

#### Conditional Formatting Rules Priority

Manage the priority of conditional formatting rules.

#### Copy, Cut, Paste Charts

Cut, copy, and paste charts to move them across sheets.

#### Ctrl+A Shortcut

Select the current range with Ctrl+A.

#### Currency Conversion Function

Implemented a currency conversion function ('currency.rate()') based on the res.currency.rate model.

#### Custom Currency Formats

Create custom currency formats.

#### Dashboard Mode

Introduced a read-only mode to use spreadsheets as dashboards.

#### Data Filters

Set up filters on a range of cells.

#### Data Source Insertion

When using the Insert in Spreadsheet button, previews of existing spreadsheets are displayed to provide more context.

#### Date Filter Offset

Match data to date filters with an offset.

#### Date Formatting

All dates are now stored as integers.

#### Date Global Filters

Select any year as a global filter.

#### Default Date Filter

Improved the default date filter to select a period relative to the current one.

#### Delete Lists/Pivots

Remove a pivot or a list from a spreadsheet.

#### Dynamic Formula Format

Dynamic format management was introduced: create formulas whose final result's format depends on the inputs of the formula.

#### End Content Keyboard Shortcuts

Jump to the end content in any direction with the 'Control + Arrow key' keyboard shortcuts.

#### Financial Functions

Added 30+ financial functions.

#### Formula Brackets/Strings Readability

Increased the color contrast of formulas highlights to improve their readability.

#### Full Column/Row Range

Added support to the full column/row range.

#### Gauge Chart

Added a Gauge chart type to display KPIs in Dashboards.

#### Global Filters from Pivot Header

Set global filters from pivot header cells.

#### Hide Sheet

Added the possibility to hide (and show) sheets.

#### Insert Graphs

Insert graphs in Spreadsheet with the 'Insert in Spreadsheet' button.

#### Large Numbers Format

Added a new format to display large numbers as shorted strings, e.g., 100,000 is shortened to 100k.

#### Legend: None Option

Remove chart legends with the 'None' option.

#### Link Charts to Odoo Menus

Link any chart to an Odoo menu to access it from the chart.

#### Link Popover on Mouseover

The link popover now opens on mouseover.

#### NA(), ISERR() and ISNA() Functions

Implemented the NA(), ISERR() and ISNA() functions for XLXS support.

#### Odoo Functions Prefix

Odoo functions are now prefixed with 'ODOO' to distinguish and retrieve them easily 

#### Percent Symbol Handling

Improved the handling of the percent symbol (%) in formulas.

#### Pivot Domain Edition

Edit the domain of a pivot from the Pivot Properties tab.

#### Pivot Insertion Sorting

When inserting a pivot, the sorting used on the pivot view is now preserved in the spreadsheet.

#### Pivot Names

Name and rename pivot tables.

#### Pivot Position Function

Extended the support of the pivot position function to all spreadsheets and not only templates.

#### Pivot/List Contextual Information

When inserted, additional contextual information is provided in the pivot/list name.

#### Relative Date Filter

Added a new "Relative period" (last x days) time range to the date filter configuration.

#### Return Date Format

When the pivot header function returns a date as a day, its numerical value is returned and, the day date format is set on it.

#### Scale Charts on Time Series/Numbers

When the labels of a chart are formatted as dates or numbers, the chart displays data as Time Series and scale accordingly.

#### Scorecard Chart

Added a Scorecard chart type to display key KPIs in Dashboards.

## Studio 

#### Approvals Flows

Integrate customized approval flows with Next Activities so users can easily find records on which their approval is required. Approval flows now have a specific responsible user to whom an Activity is assigned when a user requests an approval.

## Subscriptions 

#### Sales Order Object

Subscription Orders are now based on the Sales Order object to benefit from all its features: renew, upsell, check the MRR, etc., directly from the Sales Order.

## Surveys 

#### Action Helper

The action helper, which appears when no surveys are found, now offers to load one of the three template surveys to start testing the app right away.

#### Backgrounds

Customize surveys and live sessions with background pictures.

#### Conditional Display Rules Propagation

When copying a survey, its conditional display rules are also propagated to the copy.

#### Image Zoom

Zoom in on images when taking a survey.

#### Kanban View

Improved the design of the Kanban view to provide a better overview and jump into any result.

#### Live Sessions

End messages are now displayed on live sessions.

#### Question Layout

The question layout is decided by rules to make the form as neat as possible.

#### User Input Promenade

Review participants' results and collaborate on those documents

## Time Off 

#### Accrual Plans: Transfers

Choose how many days should be transferred at the end of the year in Accrual Plans.

#### Approvals

Approvals menu is automatically displayed when users have time off or allocation requests to approve, without requiring new access rights.

#### Avatars

When time off is created for more than one employee at a time under 'all time off', their avatars are displayed.

#### Cancellation

Users can delete their own approved and refused time off, provided they didn't start yet.

#### Dashboard

Delete not yet approved time off requests from the dashboard in year view.

#### Default Time Off

When creating a new time off request, the default time off type selection is now based on the order of the time off types under configuration.

#### Department Filter

The department filter on the Time Off overview now displays members of subdepartments.

#### Public Day Off

Time off requests will be canceled and granted back if a public day off is created on that day.

#### Start Tour

Added a Start Tour.

#### Stress Days

Create 'Stress days' where employees cannot ask for leaves.

#### Stress Days by Department

Stress days can be linked to a department.

#### Time Off Types

Added default filters to only show relevant records when using the 'time off' and 'allocation' smart buttons on the 'time off types' configuration screen.

#### Validation

Improved the error message appearing when two time off periods overlap.

#### Validity Date

From the dashboard, view the Validity Date of your available leave allocations.

## Timesheets 

#### Time Off Block

Block employees from creating or modifying timesheets linked to time off.

#### Grid View

View the remaining hours of projects, tasks, and prepaid sales order items in Grid view.

#### Lock Dates

Block the encoding of an user's timesheets based on the date of their most recently validated timesheet.

#### Open Reporting

All Timesheet users can access the reporting menu and view the available information to their access rights level.

#### Overtime: Contracts

Contracts are taken into account when calculating overtime. 

#### Reports from SO/Invoice/Ticket

Get reports of timesheet entries from sales orders, invoices, and helpdesk tickets.

#### Tickets

Log time on helpdesk tickets from the Timesheets app.

## Website 

#### Analytics Tool: Plausible.io

New analytics dashboard that works out of the box for SaaS users.

#### Backend View

Access the backend view of products, events, and appointments in one click with a new button.

#### Controller as Homepage

Define website homepage in settings: choose a dynamic page such as Event or Shop.

#### Cookie Consent

Ask visitors for their consent before storing non-essential cookies on their devices.

#### Dynamic Events

Showcase events anywhere on a website with the Dynamic Events building block.

#### GIF Shapes

Shapes can now be used with GIF images. 

#### Grid Layout

Enable grid mode on a building block to get full control of its elements.

#### Helpdesk

Add a helpdesk page when creating a new website.

#### Image Hyperlinks

Fixed adding hyperlinks to images.

#### Image Shapes: Devices

Use the new category of image shapes called 'Devices' to make images look like they are in a device (phone, tablet, computer).

#### Live Chat Icon

The Live Chat tab has been replaced by a floating icon.

#### Mega Menu Transparency

The mega menu can now use a transparent background color.

#### Mobile View Edition

Edit websites in Mobile view.

#### Preset Phone Country Code

The phone country code is now preset using GeoIP information on the Website contact form.

#### Propagate Language

Catch a visitor's language in the CRM, Helpdesk or Calendar (Online Appointments) app based on which language version of the website they are browsing when submitting a form.

#### Scroll Animations

Added scroll animations.

#### Settings

Cleaned up website settings and added new product comparison prices.

#### Site Menu Restructure

See all website pages and objects at a glance with the Site menu.

#### Social Media Building Block

Added a social media building block to manage all social network links easily.

#### Steps Snippet

Change the connectors' style on the Steps building block.

#### Visibility: Mobile and/or Desktop

Choose if a building block should be visible on mobile only, desktop only, or both.

#### Website Menu

The backend and frontend menus were merged to navigate between the two smoothly.

### Odoo Experience on YouTube

 1. Use the live chat to ask your questions.

 2. The operator answers within a few minutes.

 Watch now
