# Odoo 15 

## Release Notes
 Try it now

## Accounting

#### Chart of Accounts

Revamped account form view. Track history of changes in a chatter.

#### Aged Reports

Get amount in currency, explicit column labels. Export Amounts and Currency in two distinct columns.

#### Entries

Set a Default account in settings for automatic entries such as: currency exchange rate differences from payments, bank suspense accounts from bank sync, internal transfer accounts etc. Button added to easily set an entry as checked (be it a customer invoice, a vendor bill, or any journal entry). The changes are logged in the chatter.

#### Currencies

See conversion rate and its reverse in a revamped currency form view. Prevent confusion in currency by getting the company currency on the form.

#### Financial Reports

Control Domains on Financial Report Lines are added as a debug feature to find accounts erroneously counted multiple times (or even missing ones).

#### Follow Up

Personalize the follow-up email template subject line.

#### Fiscal Year

Gave the Lock Dates Wizard an updated design; Lock Date types are more easily differentiated from one another with tooltips.

#### VAT

Odoo supports companies that must charge foreign VAT, and declare VAT in foreign countries.

#### Payments

Navigate and distinguish Journal Entries from the related payments or bank statement lines. Record payments in list views to make their total accurate. Automatic reconciliation for both payments of internal transfer; Transfer payment from every partner category. Exclude partners from the aged reports thanks to a checkbox on account. Batch payments are more intuitive with new fields, filters and screens optimisation. Prevent user to select draft payments in the batch.

#### Journals

Payment methods management on bank/cash journal improved, manage several manual methods to allow filtering by payment card owner. Optionally specify outstanding accounts for each payment method.

#### Reconciliation 

Revamped reconciliation tool. By default, Odoo will partially reconcile invoices/bills. Fully reconcile invoices/bills in case of underpayments thanks to payment tolerance mechanism and specify the counterpart accounts for the difference.

#### Invoicing

Accrued entries, such as Invoices to be Issued/Received, can get easily generated from purchase orders & sales order actions.

#### Taxes

Carry over amounts from one period to the next on tax reports and audit those in debug mode. Break down tax groups on invoice to display intermediate subtotal (in case of discount or to manage withholding taxes.

#### Vendor Bill

The Accounting Date is the end of the month following the tax lock date or creation date. 

## Accounting - Localizations

#### Belgium

XML export for VAT added. Tax Report Line amounts carried over from one period to the next to ensure non-negative amounts in any grid; Non-deductible VAT posted on the same expense account as the main expense.

#### China

Vouchers added.

#### EC Sales List

The EC Sales List report replaces redundant versions of each country, meaning less menu items within multi-company environments.

#### Italy

DDT implemented; Italian Tax report with its carryover lines now available. Amounts carried over from one period to the next (grids VP8 and VP9).

#### Luxemburg

Manage declarations to eCDF, both Annual Accounts and VAT.

#### Norway

Balance Sheets and P&L. EHF3 format now supported.

#### Sepa

Sepa version can now be specified on journal level. Get national standard references on payments, if existing.

#### US

Made 1099 reporting easier for companies doing business in the US.

#### Czech Republic 

CoA, Fiscal Positions, and Taxes added.

#### France

Import FEC Files into Odoo. Amounts carried over from one period to the next (grids 27 and 22).

#### Germany

Get functionality for German tax report submissions to Elster.

#### Netherlands

Simplified tax groups configuration. There will now be one Tax Group per Tax Rate. Added e-Invoicing format.

#### Peru

Added EDI integration.

#### Slovakia

Added functionality for CoA, Fiscal Positions, and Taxes.

#### Taxes

Consolidate tax reports in multi-company environments.

## Appointments

#### Invitation 

Create custom invitations in the calendar and share it with guests.

#### Share

Share availability from the calendar. 

#### Slots

Define a from - to date in appointment types. 

#### Leads/Opportunities

Get leads/opportunities from a booked meeting. 

#### Website

Display appointment types as a selection or in a kanban mode. Building block for appointment.

## Appraisal

#### Skills

Skills integration in appraisal.

#### Survey 

Manage appraisal surveys (or feedback) and track answers in the Appraisal app.

#### Access Rights

Secure the Appraisal app with improvement in access rights for employees and managers.

#### Mobile

Revamped UX to fit mobile devices. 

## Approvals

#### Activities

 Activities have to be created through the Approval flow, and not manually to prevent locked situations.

#### Approvers

For each approval type, define if the approvers are mandatory.

## Attendance

#### Extra Hours

Count extra hours from attendance.

#### Time Off

Convert extra hours into Time Off.

## Barcode

#### Picking

When opening a receipt/delivery, allow users to create a batch when there are several ready for the same supplier/customer. Edit quantity and packaging. Scan a product to filter pickings by product. Smart Batching: Suggest creating a batch when several pickings for the same customer/supplier are due; Picking Notes are displayed as a popup when a user starts the picking.

#### Locations

From the home screen, scan a product, and see where it's located. In a picking, click on the edit icon to see all locations of a product.

#### GS1-128 Parser 

Added the ability to scan GS1 barcodes. When scanning one barcode, set product, quantity, SN/LOT, weight, size and/or dates all at the same time.

#### Lots/SN

Improved both the view and handling of lots/SN.

#### Package Type

Define package type through barcode scanning.

## Calendar

#### Google Synchronization

Improved synchronization of recurring events.

#### Outlook Connector

Improved synchronization of recurring events.

#### Reminders 

Get notified when new appointments are booked (or cancelled) by following the appointment type; Customize the calendar reminders.

#### Scheduling

Schedule the slots by utilizing opening hours.

#### Sharing

Create custom links containing specific types or employees.

#### Usability

Improved the Calendar UI.

## CRM

#### Contact Form

Catch user info when they submit the contact form.

#### Opportunities

Spot potential duplicate leads through a stat button. See all opportunities, even the lost ones, attached to a contact through the stat button. Related documents of a lead are added to the remaining lead in the merge. 

#### Lead Scoring

Predictive Lead Scoring feature now completely replaces the Lead Scoring feature, which has been deprecated. Get relevant company results on all Lead Generation requests. Specific salespeople can be opted out of the automatic lead assignment.

#### Outlook Connector

Log their own email responses into the chatter to keep conversations centralized.

#### Reporting

All users can now access their own reporting section where they can see as much information as they have the access rights to see.

#### Sales Teams

Assign a salesperson to multiple teams. Dispatch and assign leads based upon rules.

#### Contact

Search for contacts by phone numbers without needing to get the formatting right.

#### Gmail Connector 

Get insights on companies through the IAP integration; Create leads and tickets from emails received in the personal inbox; Create and search contacts to log emails on their chatters.

#### Mail Connectors

Both the Outlook and Gmail add-ons now support all available Odoo languages. Tasks, Leads, and Helpdesk tickets can be created from emails sent to personal email addresses in Outlook, or from the Gmail Inbox.

#### Rental 

Creating either a Quotation or a Rental from an opportunity will display the same window.

#### Sales Forecast

Build the sales forecast by dragging and dropping opportunities between months. The deadline will automatically be updated to the last day of that month.

## Contracts

#### Salary Benefits

Create a next activity on the contract's benefits to perform actions linked to the requested benefits from salary.

#### Contracts

Manage all contracts of one employee in a contract report.

## Data Cleaning

#### Merge Actions

Add a merge action on any model.

## Discuss

#### Channels

Invite people as a new member of channels. Simplified channel notifications. Removal of channels as followers and removal of mail channels.

#### Meeting

Users can now start a meeting and invite external guests to the video call within the Discuss app.

#### Chat Members

Display the list of members that belong to any given channel. 

#### Usability

Improved the Sidebar UI to make channels, chats, etc. much easier to find. Get user avatars in the DM/Group DM section in the sidebar.

#### Shortcut

Start chatting with other users from the command palette on the home (ctrl +k).

#### Direct Message

Create a group from a Direct Message.

#### Message

Edit or delete a message. Add a smiley as a reaction to a message. 

#### Voice & Video

Voice and video calls are now available. Users can change displays and share their screen to members.

## Documents

#### Document Link

Documents can be uploaded through the Employee application for every employee (not only employees linked to a user).

#### Document Edit Rights

Workspaces can be configured for the documents to be read (and edited) only by the owner of the file.

## eCommerce

#### Address

Manage several user email addresses in case this user has several postal addresses. 

#### Cart

Choose the option to either redirect customers to the checkout page, or leave them on their current page, when they add an item to their cart.

#### Event Registrations

Define a specific time for event registrations to start and end.

#### Gift Cards

Offer customers the chance to purchase gift cards for the online shop.

#### Journal Entries

Get today's date rather than the first available date when trying to post before a lock date; Get payment or Bank Statement Line from a stat button on journal entries; Get unique Journal short codes on a former journal entry duplication.

#### Reporting

The EC Sales List report replaces redundant versions of each country meaning less menu items within multi-company environments; Exporting both the partner and general ledgers to CSV will distinguish the amount in currency (and the foreign currency) in two separate columns; Improvements made to Report Layouts, such as optimizing the information on screen and exports.

#### Vendor Bills

Invitation to activate a currency before posting a vendor bill if currency is currently inactive, especially through OCR / EDI.

#### Prices

Display the price per unit on the products as required by many countries. Added a new price range filter on the /shop page.

#### Shop

New design for shop and product pages. Enable a recently viewed products section. Introduced new, beautiful design for the products building block, as well as the ability to show discounts.

#### Products

Users can now decide which attributes should be visible on their eCommerce web pages.

#### Wishlists

On eCommerce shop, allow customers to sign-up for availability notifications on Out of Stock products.

## eLearning

#### Attendees

See the last time the attendees were active on a particular course. Also, congratulate attendees who've finished the course with a new filter on the attendees list.

#### Usability

Manage sections directly from the website.

#### Courses

Removed the automatic creation of link trackers on course content. The process can be done manually, if needed.

## Email Marketing

#### New Editor

New editor for the mail composer to benefit from all the features of the website builder.

#### A/B Testing

Test multiple versions of the mailings on a sample of the recipients, send the version that performs best to the rest of the recipients.

#### Mailing Lists

Create mailing lists on-the-fly, and move contacts between lists using the Add to List button. Check health/quality with KPI on kanban card.

#### Feedback

Receive feedback in the chatter after each mailing test is sent out.

#### Scheduling

Schedule and overview the mailings through a calendar view.

#### Subscription

Send mailings to the sales subscriptions to get in touch with active customers.

#### From Jinja to QWeb

Email templates have been ported to QWeb templates. The new composer allows you to edit emails in their final rendering, making customizations more robust, as you dont have to edit code anymore.

## Employee

#### Employees

When a manager/coach is archived, concerned employees are not linked to that manager/coach anymore.

#### Departure Reasons

Custom departure reasons can be added to Employee configuration.

#### Creation of Appraisals

Appraisals can be created through the action menu for a given Employee.

#### Languages

Let employees edit their languages from the salary configurator or profile.

#### Work Permits

Get work permits into the Personal Documents tab. Prevent work permit validity overpass with the next activity.

## Expenses

#### Expenses

When creating a similar expense twice, the user will be asked if it is a duplicate. A note is added in the chatter. Create expenses in foreign currency. Categories with a defined cost only allow users to edit the quantity.

#### Expense Reports

Save time by batch editing the expense reports.

## Events

#### Attendees

Autofill the registration form based on historic data. Set access rights to register attendees without being able to edit events. Customize and print the tickets for events. Badges can now be printed as foldable and/or A4.

#### Exhibitors

Manage the booth you rent during the events. Handle Sponsors and Exhibitors without needing to activate tracks.

#### Social Posts

Schedule Social Posts based on the timing of the Events.

## Field Service

#### Invoicing

Invoice the time spent on the tasks at an employee rate.

#### Time Off

Get an extra warning when a service task is planned during an employees time off.

## Fleet

#### Fleet Management

Manage new fuels (Plug-in Hybrid Diesel, Plug-in Hybrid Essence, Full Hybrid Essence, CNG, and Hydrogen); Manage change of driver date through an activity based on the end date of the current driver. Modify the filter by car (or bike) in the dashboard; Fleet Managers are now assigned to vehicles and not to a model.

#### Reporting

Get KPI with a pivot view on Vehicle, Contracts, and Services.

#### Employee

Get the employee profile from the vehicle form.

#### Salary Configurator

Sort the vehicle in the salary configurator by category, and display more information.

## Helpdesk

#### Tickets

Close inactive tickets automatically. Track the state of the returns, repairs, refunds, and Field Service tasks in the tickets' chatter.

#### Leads

View the other tickets of the customer directly from the ticket. Convert the tickets into leads.

## Inventory

#### Inventory

Complete redesign of inventory adjustments (also on Barcode app). Cyclical inventory by location; Filter by suspicious inventory: Identify negative stocks and identify double Serial Numbers; See history on each quantity; Easily solve conflicts; Count anything, anytime, without inventory adjustment.

#### Picking

Improved delivery slips and picking operations. On delivery slips, ordered quantities and backorder lines are now displayed in the correct order. Easily edit quantities done in pickings. Select Closest Location as a removal strategy to pick a given product category. Select several ready moves of a given picking type and group them in a new batch.

#### Routes

In multi-step routes, propagate carrier and country of destination, and allow for printing a label at the chosen step.

#### Forecast Report

Reserve, unreserve, and change priorities directly in the report. Display customers and total amount of sales orders.

#### Packagings

Order and sell products based on packaging. Display packaging in warehouse moves. Link packagings to package type for advanced putaway rules.

#### Product

Favorite the most used products. When searching through autocomplete, they will always appear in the first hits. Select the format of product labels, and allow users to print extra text on labels.

#### Reception

View allocation for received products. Assign received products to deliveries. Print labels for assigned products.

#### Reordering Rules

When creating reordering rules, use multiple quantities with BOM quantities. For Manufacturing Orders, and Purchase Orders instantaneously triggered from reordering rules, you can keep track of the source document.

#### Reservations

Reservations can now be automated, manual, or triggered X days before the scheduled date. Mass reservation moves based on filters.

#### Putaways

Define putaway rules for package types. Define storage categories with capacities by weight, product, or package type to redirect the products.

#### Units of Measure (UoMs)

Improved category management for units of measure.

#### Replenishment

Added vendor info in the replenishment to have a direct overview on the list of vendor prices and delays.

## Manufacturing

#### Analytical Accounting

Set an analytical account on Manufacturing Orders and track costs of production in real-time.

#### BOMs

Use copies of existing operations when creating new BOMs. Or change the BOM of an existing operation. On the BOM, just like for components, apply by-products and operations on specific variants.

#### Component Forecast

Odoo will consider the Component forecasts even for draft Manufacturing Orders. Component Availability displayed on the MO list and form views. Total Manufacturing Order expected and actual time spent are also displayed.

#### Quantity

No longer required to specify component location in a multi location environment. Lock Quantities to consume active by default. Close a Manufacturing Order without consuming anything.

#### Kits

Be notified on pickings if components are to be delivered as part of a kit.

#### Serial Numbers

When mass producing serial numbers, generate multiple manufacturing orders at once, and confirm in bulk, for faster encoding.

#### Work Orders

New work order status related to material availability.

#### MPS 

New Row options to consider demand last year and the year before. Filter and favorites added to main menu. If a product is listed on the MPS, its demand planning can now also be done from the product form directly. All BOM components of a product can now also be added to the MPS in one click.

#### Product

When editing a product form, receive a warning if the chosen Internal Reference already exists.

#### Cost Reporting

Consider the cost of by-products by applying cost shares on the BOM, or on the Manufacturing Order. Adapted MO cost analysis report format for by-products cost valuation, as well. New Production Analysis dashboard to analyze manufacturing costs and general performance over time. Pivot and Graph Views added to Scrap Analysis.

#### Subcontracting

New route to resupply Subcontractors on order. Better management of subcontractor resupply dates by using manufacturing lead time on subcontracted products. Flexible consumption is now also possible on subcontracting receipts, as per BOM configuration.

#### Workcenters & Operations

Multi-edit and other improvements, such as adding more information, added to both Work Centers and Operation listviews.

## Payments

#### Authorize.Net

Support ACH payments (USA only). New, clean payment flow within Odoo for shoppers, rather than a page redirect.

#### Adyen

Improve the handling of payment errors with PSP Adyen. New, clean payment flow within Odoo for shoppers, rather than a page redirect.

#### Buckaroo

Improve the handling of payment errors with PSP Buckaroo.

#### Mollie

Mollie available as a new Payment Service Provider.

#### Payment Link

Add payment acquirer selection as a dropdown to the payment link wizard.

#### Refunds for Adyen

Allow users to do refunds (partial and/or full) for Adyen. This will be implemented for other payment service providers in the near future.

#### Stripe

Possibility to tokenize user cards using a redirect.

## Payroll

#### Belgian Localization

Get hospital insurance as a standard benefit. Allocate legal time off according to the employee's previous year and current working schedule. Added a new wizard to define an employee's language, based on the country recommendations when generating payslips.

#### Commissions

Introduce a structure to manage commissions and import amounts easily.

#### Employee

Display the age of the employee directly on the Payroll screen.

#### Part-Time

Use the part-time wizard for every change of work schedule for employees.

#### Payslips

Improved the layout of the Payslip PDF. Changed the way payslips are batch created to prevent timeouts. Prorate employee benefits based on real worked days, as well as salary deductions (or reimbursements). Implemented a mechanic for withholdings after a salary advance from the company; Manage students pay right in the app. Improvements made to managing negative net. 

#### Reporting

Added a new Social Audit report into Reporting; Analyze work entries to perform some legal actions or review statistical analysis.

#### Salary Attachments

Salary attachments are now managed in standard applications, and available to all countries. They are now linked to the employee, and not on the contract, itself.

#### Time Off

Improvements made to the relationship between contract and additional time off allocation. Easily manage time off taken after the pay closure.

#### Work Entries

Select the right work entry type to manage part-time employees work entries; Regenerate a specific employees work entries.

#### Working Schedule

New filter for employees who change their working schedule during the year.

## Planning

#### Time Off

Get a warning when an employee is on time-off at the moment of the shift.

#### Resources

Plan shifts for human and material resources. At a glance, view the job title and current allocation of the resources, thanks to the progress bar in the Gantt view.

#### Sales Orders

Generate shifts automatically when confirming the Sales Orders. Plan all of them to the right employee(s) in one click. The roles, time off, workload, and working hours of the employees are automatically taken into account, as well as the time left to plan on the Sales Orders. 

#### Shift

Never have conflicts when copying plans from the previous week. The time off, workload, and working hours of the employees are automatically taken into account, as well as the time left to plan on the Sales Orders.

#### Skill Search

Identify the perfect person for a shift by searching on the employees' skills.

## POS

#### Login

Secure log-in experience for the PoS session. 

#### Cashier

From the cashier form: refund customers and track it; import quotations and sales orders; request a down payment and later settle the order; access Cash In and Cash Out; get information about a product (inventory, financials, replenishment, product attributes, optional products,etc.); check the customers' due amount and settle the due as a classic order; reach the customer's back-end page to get further information.

#### Coupons

Use coupons, and their usage, into PoS. Created links between PoS barcodes and coupons. Sell Gift Cards to the customers. Reimburse them with a voucher when needed. Allow them to pay with both in the shop.

#### Customer

Write a custom 'Customer Note' for a specific product to print on the receipt and invoice.

#### Margins

Get margin on the PoS orders, and within the Reporting dashboard. 

#### Mobile

Enjoy a freshly updated and mobile-friendly version of the PoS Shop and Restaurant! 

#### Orders

Find all the ongoing and paid orders within the same screen.

#### Payment

Customers can choose to 'Pay Later' or 'Pay Partially' for their orders, and they can get a corresponding invoice stating the right due amount.

#### Product

Get product packaging in PoS! Allows products to be configured to manage quantities and several barcodes at the same time.

#### Sessions

Limit the number of products and partners loaded at the opening of the session in order to increase the rapidity (and load the rest in the background).

#### Shipping

Enable this new feature in the settings to ship products later for customers.

#### Usability

Made a variety of UX and UI improvements, such as: spot the cashier tab with a grey favicon, extra information on invoices and receipts, tips improvement, several opened sessions warning, etc.

## Project

#### Tasks

Assign multiple users to the tasks, organize the tasks into their own Getting Things Done (GTD) pipeline, and create private tasks that are only visible to you. Track the stages and deadline changes of the dependent tasks in the chatter of the tasks.

#### Sharing

Share the kanban and list views of the projects with portal users, and grant them edit rights on tasks.

#### Gantt

At a glance, view the project's milestones in the Gantt view. Determine the order in which to perform tasks. Create task dependencies from the Gantt view. Reschedule dependent tasks automatically when there is an overlap: the time off and working hours of the employees are automatically taken into account. 

#### Portal

View more information about the Sales Orders and Invoices linked to the tasks in the portal.

#### Project

Get an overview of the current status of the projects, and keep track of their progress with the Burndown chart. Also, see, at a glance, the profitability of the projects in relation to their budget, costs, and revenues. Define stages on projects.

#### Upsell

Get an order upsell activity when a prepaid service is close to being fully consumed.

## Product Lifecycle Management

#### Products

Manage document changes for the product template and its BOM(s) separately.

#### 

#### Manufacturing Orders

Track changes made on Manufacturing steps : Type/Addition/Removal. For a draft manufacturing order with an outdated BOM, update it with the latest BOM in one click. For a confirmed manufacturing order, be notified when a new version of the BOM exists.

#### ECO

Share ECO stages between ECO Types. Apply changes for several ECOs at once. Activities triggered for Approvals. Once changes are applied, further modifications are locked for this ECO. 

## Purchase

#### Dropshipping

Dropship products using purchase agreements.

#### Portal View

Improved the visibility of purchase orders in the portal view.

#### Replenishment Overview

From the replenishment, have a clear overview of lead times, vendors, and monthly sales.

#### Commissions

Added purchase type to distinguish procurement and commissions.

## Quality

#### Quality

Differentiate between Quality Checks and Manufacturing Steps. Updated Quality Check PDF report, with both Internal and External templates available out-of-the-box. New Quality Check Point Type that allows for checks per stock move line. When detailed operations are activated, a new view is also available.

#### Product

Apply Quality checks on product categories.

#### Worksheets 

Design worksheets from scratch, and add them to your quality points. Or, during onsite interventions with the Field Service module.

#### Usability

Search by lot/serial (and finished lot/serial) from the Quality Check list view. Product and Lot/Serial Menu added to Main Menu.

## Recruitment

#### Mobile Kanban

New Kanban view on mobile for job sources.

#### Jobs

Get online jobs management with both apps - Website and Recruitment. 

#### Refusal

Send refusal emails using templates based upon refusal reasons. 

#### Reporting

Added an option to say which stage should be used to count Hire Applicant on the recruitment reporting. Also changed the referral option label.

#### Usability

Made improvements to usability, added an application summary field, and users can manage multi-applications.

## Referrals

#### Alerts & Notifications

Updated the alerts a user receives. Added a new feature to email notifications.

#### Rewards

Users are now able to archive Rewards.

## Repair

#### Notes

Differentiate between Repair Order Notes and Quotation Notes.

#### Scheduling

Priority and Scheduled date added to Repair List and Form views.

#### Workflow

Improve the repair workflow with a more flexible interface and tracking of product moves.

## Sales

#### Pricelists

Easily configure Pricelists with improvements to item form views, including a simulation.

#### Terms & Conditions

Default content will now appear for Terms & Conditions as a web page, with a default link to it on every SO.

#### Product

Get pictures for the products based on their UPC/EAN barcode numbers. You will need a Google Custom Search API key.

## Sign

#### Email

Users can now send a sign request using templates to the employee after signing a document, and add attachments to an email request. Content of the email can differ between recipients and followers. Display if an email has to be sent, and make it easier to know when emails have been sent.

#### Documents

Upload multiple files at the same time, and process them one after another, rather than each one individually.

#### Mobile

Improved the usability of the Sign mobile app to facilitate the signature experience on-the-go.

#### Usability

Added the ability to use the layout of fields from a previous file when creating a new one. Set colors for each role on a document template. Added the ability to choose the text alignment locations.

#### Responsible

Add a responsible person to a document that needs to be signed.

#### Terms & Conditions

Define specific Terms & Conditions in the settings of the Sign app. These will then be displayed on every email sent to recipients.

## SMS Marketing

#### D+1 Report

24 hours after an SMS Mailing is sent, a recap is sent to its responsible party to show its first results.

#### SMS

Resend all failed SMS Text Messages in one go.

## Social Marketing

#### GIFs

Upload and see GIFs on Facebook.

#### Instagram

Link an Instagram page and start posting from the Odoo dashboard.

#### Multi-Company

Link social accounts to individual companies within a multi-company environment.

#### YouTube

Link a YouTube channel and start posting from the Odoo dashboard. Post a video to Youtube, and include a link in the other posts.

## Spreadsheet

#### Cells

Automatically sum a cell range with ALT + =. Insert and remove cells. Select multiple cell ranges by holding CTRL while in edit mode. Use directional arrows to make quick changes to cell content. Edit cell contents from the function bar. Hide sheet columns or rows.

#### Drag & Drop

Drag and drop columns and rows, cell content across the sheet, and ranges when editing a formula.

#### File Uploader

Display a loader during file uploads and error messages, if needed.

#### Formatting

Improved conditional formatting and icons formatting. Also improved underline formatting. Insert links to URLs, other sheets, and Odoo menus. New feature allowing for finding and replacing values. Improved the usability of column/row resizing. Responsive scroll when selecting ranges.

#### Formula Assistant

Introducing Formula Assistant!

#### Listview

Insert any listview directly into an Odoo spreadsheet.

#### Accesses

Spreadsheets can now be accessed in Read-Only mode.

#### Sidepanels

Updated the UI design for a variety of sidepanels throughout the app.

#### Smart Chart

Updated Smart Chart creation and customization.

#### Sorting Options

Added further sorting functionality on columns and ranges.

#### Collaboration

Collaborate with teammates on Spreadsheets. Collaborative Tags only displayed when hovered over.

#### XLSX Export

Export Odoo spreadsheets as XLSX files.

#### Pivot

Access pivot properties directly from the topbar; Filter pivot data on any month during the last 3 years; refresh all pivots at once. Drill down to records from pivot aggregates.

## Studio

#### Model

Improvements to the Model Configurator. Including automatic reporting views and colored Kanban cards.

#### Stat Button

Create custom stat buttons from many2many relations.

#### Fields & Lists

Activate Studio from the optional fields dropdown of the listview through add a custom field, and automatically add custom listviews to the optional fields dropdown. Easily set up a custom 'lines' one2many field.

## Survey

#### Live Sessions

During a live session, hosts may go back to previous screens.

#### Certifications

Added new certification templates.

#### Results

Results are displayed per section on a chart at the end of the survey.

#### Sharing

Share the success with the Odoo certification directly from the results page.

## Time Off

#### Access Rights

As a manager, get access to employees with implicit access rights.

#### Salary

Create allocation of requested Time Off from salary configurator.

#### Dashboard

Calendar Year improvements include: updating calendar and Gantt views for unapproved time off. 

#### Configuration View

New Time Off type configuration view.

#### Mobile

Manage Time Off requests more easily on mobile.

#### Accrual

Define accrual plan and set it on employees to manage time off Allocation.

#### Documents

Get justification for some time off types, like sick days.

#### Payroll

Get direct access to the employees Time Off in the Payroll application.

#### Allocation

The validity mechanic is now set on the allocation, instead of the Time Off type.

## Timesheets

#### Time Off

Generate timesheet entries for global time off automatically.

#### Overtime Overview

At a glance, view the overtime of the employees in the Grid view.

## Usability

#### App Menu

Apps reordered for clarity in the app menu.

#### Import

Can now be interrupted and their progress is displayed live. Added indicators for the exact erroneous one2many row(s) on failed imports. Added advanced previews and the ability to skip erroneous records in the Import Wizard.

#### Ratings

Ratings reports are now based on the response date.

#### Auto Save

Records are automatically saved when left, and can be directly edited from the saved mode.

#### Company

Enrich internal contact upon creation.

#### Filters

A scroll down bar is added in filters when the list of records to filter is too wide to be displayed on the screen. Group by m2m fields.

#### Forms

Windows of company data & partners have been harmonized.

#### Avatars

Users without a personalized avatar will now have a colored avatar with their initials. Now available for many2many fields, and available/clickable in editable form views. Automatically set the avatar on new many2one fields to users.

#### Listview

Improved colored decorations on listviews. Added activities in several lists.

#### Mobile

Added a short tap on Calendar for event creation (DAY, WEEK) or navigation (MONTH, YEAR).

## Website

#### Website Configurator

New Artificial Intelligence engine to create full websites based on the industry and preferences.

#### Shapes

Introducing animated shapes to give even more life to the website!

#### Animations

Add animations to text, images, and columns.

#### Background Images

Use extra-tall or extra-wide images as backgrounds.

#### Boolean Toggle Widget

Improved the readability of the boolean toggle widget.

#### Building Block

Added several templates to choose from for the Accordion, Masonry, Steps, and Products building blocks. Select a custom color for their link buttons. Added a brand new building block for receiving donations. Define conditional visibility. 

#### Coupon Sharing

Added the ability to share available coupons via email, link, and social media.

#### Default Form Values

Set a default value on form inputs.

#### Document

Easily add documents to the website,and choose the right design for the customers/visitors to download them. Change the assignee, priority, and state of a document with dedicated hotkeys.

#### Double-Indexing

Prevented double-indexing of home pages with multi-language options.

#### Navigation

Improved keyboard navigation within the dropdown menu. Select multiple companies at once in the company switcher. Navigate through menus with the command palette. Improved command palette design and footer.

#### Forms

Added support for users to build forms with fields that appear conditionally.

#### HTML

Added functionality for users to enter custom embed HTML codes in a building block.

#### Search

Added the ability to use fuzzy search in website search bars for products, blogs, etc. Add the option to sort items, via Searchpanel.

#### Google Analytics

Updated the Google Analytics integration to accommodate Googles most recent update.

#### Design

Choose a gradient for background, text, and button colors. The color picker comes with a gradient editor, with linear (or circular) gradients that you can customize easily. Introduced more control to the Grayscale palette. Added support for multiple colors.

#### Image

Improvements made to the image size display. Color feedback on image size; Clicking on an image will display an overlay around the selected column; New progress bar for image upload. Add shapes (some animated) onto images.

#### Newsletter Pop-Up

Now, customize the Newsletter Pop-ups size, timing, backdrop, and more.

#### Pie Charts

Percentages are now displayed in pie charts.

#### Pop-Ups

Add the possibility to have a pop-up on a click.

#### Product Carousels

Promote products on a blog post utilizing product carousels.

#### Quicksearch

Quicksearch now available on selection fields.

#### Tabbed Values

Tabbing out of an empty many2one field will no longer set the first value.

#### Templates

Customize web page headers with beautiful, new templates.

#### Themes

All themes have a new, more modern look! Themes have been renamed to contain industries. The Options tab is now the Theme tab.

#### Usability

Auto-select and double-click to select hexa and RGB code in the color picker. New visual indicators to show when a user enters full-screen mode, and explains how to exit. Option tab renamed to the Theme tab. A new widget appears after clicking on any URL, giving users the option to modify, break, or copy the link to the clipboard. The option tab is now always clickable, and explains that a block needs to be selected to style it. Applying a shape to a block now opens the Shapes Selector. Default Values can be set for form inputs. Many2one fields are now supported in the editor. Removed API key to use Google Maps. Deleting a block will auto-select the children of the block, as well.

#### Website Headers

Added new options to style web page headers.

#### Web Logo

Added a new default website logo.

#### Video Building Blocks

Added 12 suggested videos to make choosing a background video easier; Carousels are mobile-friendly! A new featured, adapted style, centered images, etc; Automatically given a default name with the option to edit, if necessary; Image Wall and Gallery have blocks that are now prefilled with images. Buttons to add (or remove) images have been highlighted.

### Odoo Experience on YouTube

 1. Use the live chat to ask your questions.

 2. The operator answers within a few minutes.

 Watch now
