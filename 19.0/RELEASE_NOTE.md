# 
 Odoo 19

## Release Notes
September 2025

## Technical

Cached data

Data fetched during browsing is stored in cache so that subsequent visits to the view load directly from the cache, improving navigation speed.

Cached translations

Translations are stored in cache with the aim to speed up the display.

Control panel display

The search, the view switcher, and all elements located at the top of the screen are directly available without waiting for the data load, allowing searches to be directly performed.

Date format

Display abbreviated dates instead of numeric, i.e., Aug 1, 2025 instead of 08/01/2025.

Dropdown formatting

Search dropdowns now include basic text formatting to improve readability.

Import any file format

When importing files with the importer, any file format is allowed in binary fields (available from 18.0).

Incremental mass edit

When numeric fields are edited en masse in a list, use the addition (+=), subtraction (-=) , multiplication (*=), and division (/=) assignment operators to increment or decrement all values. For example, select all products and type "*=1.1" to increase the prices by 10%.

New partner autocomplete provider: Dun & Bradstreet

The partner autocomplete service has been revamped to use data from a new provider: Dun & Bradstreet. The functionality and pricing remain the same, but the quality of the data has been drastically improved thanks to the high quality database of D&B (especially for markets outside of Europe).

Parent record change

When a record's parent record is changed, its property values are logged in the chatter.

Tracking information for messages sent from list view

Get tracking information for messages sent from the list view.

## Industries

Accounting Firm

A new industry package is available for accounting firms, managing prospective clients, documents, client accounts, and billing.

Bakery

A new industry package is available for bakeries offering a variety of baked goods (available from 18.0).

Beauty Parlor

A new industry package is available for beauty parlors, including customer management, appointments, treatment follow-up, and invoicing.

Beverage Distributor

The Beverage Distributor industry has been updated to include excise management.

Bowling Alleys

A new industry package is available for indoor activity centers such as bowling alleys.

Campsite

A new industry package is available for camp sites.

Candy Shop

A new industry package is available for managing candy shops, including online snack retailers.

Carpenter

A new industry is available for carpenters to centralize business operations from generating accurate quotes and managing material inventory to overseeing construction projects and creating invoices.

Catering

A new industry package is available for catering services, managing the entire business from client contact to on-site planning.

Cleaning Service

A new industry package is available for cleaning service companies (available from 18.0).

Climbing Gym

A new industry package is available for climbing gyms, managing subscriptions, entrance bundles, staff planning, and waiver signature.

Concert Halls

A new industry package is available for concert halls, managing live event organization, ticketing, and backstage operations.

Condominium

The Condominium industry has been updated with owner history tracking, general meeting animation and reporting, and cost distribution based on meter readings and periods.

Construction Developer

A new industry package is available for construction with more advanced capabilities.

Cosmetics Store

A new industry package is available for cosmetics businesses, managing sales, inventory, eCommerce, purchases, and customer loyalty.

Coworking Space

A new industry package is available for coworking spaces, managing memberships, bookings, events, website, and community engagement.

Dropshipping

A new industry package is available for businesses that purchase from suppliers and ship directly from the supplier to the customer (available from 18.0).

eLearning Platform

A new industry package is available for eLearning platforms that want to sell courses online.

Electrician

A new industry package is available for electricians doing regular field service jobs (available from 18.0).

Environmental Agency

A new industry package is available for environmental agencies offering ESG consultancy services.

Escape Rooms

A new industry package is available for escape rooms.

Event Management

A new industry package is available for event management, including contact management, event bookings, logistics, and equipment rentals.

Eyewear Store

The Eyewear Store industry package has been updated to include a possibility of detailed tracking of clients' medical data, sharing it via PDFs, and more.

Florist

A new industry package is available for florists managing walk-in orders, customized bouquets, and event floral designs.

Food distribution

A new industry package is available for food distribution companies.

Food Truck

A new industry package is available for food trucks and other mobile food businesses, such as ice cream trucks (available from 18.0).

Gallery

A new industry package is available for art galleries and other creative spaces.

Hair Salon

The Hair Salon industry package was updated to provide split bookings.

Handyman

A new industry package is available for handymen managing mostly small field services.

Hotel

A new industry package is available for hotels, managing several offers with multiple rooms and online booking.

HVAC Services

A new industry package is available for HVAC services.

Law Firm

The Law Firm industry package has been updated to enhance case management and add legal requirements for lawyers, including KYC assessment and a three-level signature process (available from 18.0).

Library

A new industry package is available for libraries managing all types of loans and in-house events.

Machine and Tool Rental

A new industry package is available for machine and tool rental businesses, managing rental contracts, quarantine on returns, consolidated down payment based on consumption, machine runtime monitoring, and maintenance planning.

Marketing Agency

A new industry package is available for marketing agencies (available from 18.0).

Mental Therapy

A new industry package is available for mental health professionals, managing patients, therapy sessions, session notes, and invoicing.

Metal Fabricator

A new industry package is available for metal fabricators, managing customized products and large-scale, tailored projects.

Microbrewery

The Microbrewery industry now supports excise management.

Night Club

A new industry package is available for night clubs, managing entrance tickets sales, the bar, and social media management.

Non-profit Organization

A new industry package is available for non-profits to manage donations, petitions, volunteers, events, memberships, and supporter communication.

Outdoor Activities

A new industry package is available for outdoor activities such as scuba diving, whitewater rafting, and other outdoor adventures (available from 18.0).

Personal Trainer

A new industry package is available for personal trainers and fitness coaches.

Pet Groomer

A new industry package is available for pet grooming services.

Physical Therapy

A new industry package is available for physical therapists, managing patient care, therapy sessions, treatment tracking, and billing.

Real Estate

A new industry package is available for real estate agencies.

Shoemaker

A new industry package is available for managing shoe repair and manufacturing (available from 18.0).

Spa Resort

A new industry package is available for spa resorts to manage wellness entries, massage and treatment appointments, online bookings, point of sales, and staff scheduling.

Sport Events

A new industry package is available for sport event organizers, managing events, sponsors, inventory, ticketing, staff planning, and participant communications.

Tattoo Shop

A new industry package is available for tattoo shops (available from 18.0).

Team Sports Club

A new industry package is available for team sports clubs, including the sales of game tickets, foods and beverages, club merchandising, and management of annual licenses and training sessions.

Textile Manufacturing

A new industry package has been added for textile production management that meets both standard and custom orders.

Theater

A new industry package is available for theaters and cultural venues managing performances and bar operations.

Third-Party Logistics

A new industry package is available for third-party logistics companies, handling a full range of services from customer stock management to all necessary operations.

Thrift Store

A new industry package is available for thrift stores, managing sales and employees' shifts.

Veterinary Clinic

A new industry package is available for veterinary clinics, designed to help manage pet owners, their animals, consultations, appointments, website, and inventory.

Wedding Planner

A new industry package is available to help wedding planners manage projects, customers, and vendors (available from 18.0).

Wine Shop

A new industry package is available for wine shops.

Yoga & Pilates Studio

A new industry package is available for yoga and Pilates studios.

## Accounting

Account selection

- Add descriptions on accounts to explain when to use each account.

- Default taxes on accounts are only applied on invoices and bills and not on miscellaneous entries.

- On invoices, income accounts are proposed first. On bills, expenses and fixed assets are proposed first. Both filters can be overridden if needed.

Analytic budgets in One App Free

Use budgets without installing the Purchase app, for simple budgeting without committed amounts.

Analytic subplans

The management of the hierarchy of analytic subplans in budgets and analytic reports has been improved.

Annual statements composite report

A default Annual Statements composite report simplifies year-end reporting by combining the balance sheet, profit and loss, and trial balances so users can print them all at once.

Audit reports

Create and export audit reports via Knowledge.

Asset import templates

Import templates have been added for assets.

Bank reconciliation

- The bank reconciliation interface has been simplified. Automated reconciliation models have been improved to enhance transaction recognition.

- Keyboard shortcuts are available on the bank reconciliation view.

- Draft entries can now be reconciled. Eventual automatic moves (like currency exchange or cash basis moves) are created in draft simultaneously with the reconciliation. When posting the original entry, the reconciliation is automatically confirmed as well.

- Fully reconcile or write off partially reconciled items.

Bank statement OCR manual correction

After a statement has been processed through the OCR, manually correct the Starting Balance and Ending Balance of the statement by clicking on the fields and then on the appropriate number in the PDF.

Bank transactions with PDF preview

Preview attached documents directly in the bank transaction list view whenever a document is linked to a statement.

Cash discounts

The "Always (upon invoice)" option has been added for tax reduction on payment terms with a cash discount.

Debit notes

The Debit note option in the Action menu has been replaced with a dedicated button.

Default recipient bank account

On customer invoices, the "Recipient Bank Account" field is now populated using an improved selection algorithm. If a specific payment method is set on the partner of the invoice, Odoo will select any related bank accounts for the payment of the invoice. If no payment method is found, Odoo will select any bank account of the company whose currency matches the currency of the invoice. If nothing is found, the first available bank account will be set.

Deferred miscellaneous entries

Start and end dates have been added to miscellaneous entries, allowing deferral of bills to receive and invoices to issue.

Down payment account

The Down payment Account field has been moved from Product Category to Accounting Settings.

Duplicate bill detection

When a potential duplicate bill is detected, the warning banner remains visible even after posting and the Reference field of affected bills is highlighted in the list view. Duplicate bills are excluded from automatic posting.

Exclude from follow-up

Exclude specific moves from follow-ups. Excluded invoices are ignored in both the computation and report sent.

Fiscal categories on accounts

Fiscal categories have been moved to accounts to allow different rates to be set for accounts in the same category (e.g., for fleet expenses in Belgium) without duplicating categories.

Fiscal positions

- Tax mappings in fiscal positions have been removed. Instead, each tax declares in which fiscal position it is applicable (empty means all) and declares which taxes from other fiscal positions it replaces (e.g., a 0% export tax declares it replaces national sales taxes in the context of the Export fiscal position). Taxes are by default filtered on invoices based on the fiscal position, and on products based on the Domestic fiscal position (the first in the list).

- Fiscal positions are now always determined based on their sequence, with filters applied for applicability. Multiple localizations have been simplified to a single Domestic fiscal position.

- On invoices, taxes in the current fiscal position that are replaced by others are filtered out by default, with additional taxes still accessible through "Search More".

Follow up via WhatsApp

Use WhatsApp messages on follow-up levels.

Follow-up report

A new variant of the partner ledger, the follow-up report, has been introduced. It is clearly separated from the customer statement and serves a distinct purpose: highlighting overdue invoices separately from due ones. The report is accessible from the reporting section for streamlined follow-ups.

Follow-up/partner ledger access to Invoicing and banks

Invoicing in Enterprise now grants access to the account report module and activates partner reports by default, allowing both Invoicing users and Accounting users with only "Invoicing & Banks" access rights.

Improved duplicate detection

Invoice duplicate detection has been improved with two levels of warnings for invoices and bills: red invoices that are highly likely to be duplicates that should be corrected and yellow potential duplicates that should be investigated before posting. After posting, only red warnings will remain.

Invoice email attachments

When sending an invoice by email, documents from the Documents app can now be easily added as email attachments.

Invoice analysis

The total amount (i.e., the total amount of the invoice converted in the company currency) is available as a measure in the Invoice analysis report.

ISO20022

- Define the charge bearer on individual payments when generating batch payments for ISO20022 payment files.

- The priority instruction can be specified on payments for the ISO20022 paymenth method and its variants.

- Outgoing ISO20022 payments now include the End-to-End identifier to simplify reconciliation.

KYC payment verification

The KYC verification has been improved for payment initiation from Odoo.

Light audit trail

The non-restrictive audit trail has been made available by default for everyone.

Menu and form organization and layout

- A new journal creation wizard has been added to the dashboard; it includes bank and credit card account synchronization.

- The Journal and Reconciliation models' form views have been revamped.

- The invoice line display settings are now stored separately for customer invoices and bills, allowing different configurations for incoming and outgoing invoices.

Miscellaneous journals dashboard link

On the dashboard, miscellaneous journals have a link showing the draft entries to validate manually. This link also shows entries that have been imported automatically (i.e., with an email alias).

OCR manual correction

After a document has been digitized, select any portion(s) of text to fill in any field present on the form view, including custom fields and fields in one2many lines. Automatically create multiple lines at once by selecting multiple amounts in the document at the same time.

Open on date

The Open On Date feature allows checking the status of amounts still outstanding after a company's financial year-end to ensure the accuracy of the financial statements.

Payment communication generation

Payment communication formats are made explicit with examples. The European standard has been improved. A new "numbers only" format has been added to handle countries where payment references including special characters are not supported.

Payment withholding tax

The option to apply a withholding tax directly on the payment has been added (available from 18.0).

Print & Send

Improved the layout and usability of the Send & Print wizard, the accessibility of the Print menu options, and the customizability of action reports and templates via Studio.

Professional percentage for receipts

The "Professional" percentage column that was available for vendor bills is now also available for purchase receipts.

Purchase and sales receipts

Purchase and sales receipts have been merged with invoices and bills. Purchase receipts are always available: change between bill or receipt on the vendor bill form. Sales receipts can be activated in the settings. Localizations can be set to override default taxes depending on the local requirements.

Purchase order matching

When importing vendor bills (via XML or OCR), Odoo will look for purchase order references everywhere in the imported bill (including on line descriptions) to match with existing purchase orders.

Report annotations

Annotations made on reports are visible in the report's chatter.

Reset invoices/bills to draft

Resetting an invoice/bill detaches the invoice already generated (available from 18.0).

Review invoices

Accounting users with only invoicing access rights (Invoicing or Invoicing & Banks) are allowed to post moves like invoices, but they are automatically flagged as "To review" for accountants (users with Bookkeeper or Administrator access rights). Once a posted move has been reviewed by accountants, it cannot be reset by Invoicing users anymore.

Tax report: tax tag signs

The + and - signs have been removed from tax tags on tax reports; inversions are now handled directly on the report lines.

Tax return

A new tax return feature has been introduced, supporting fiscal return obligations and deadlines and automated validation checks to ensure accurate filings. The feature is customizable to meet localization needs across different regions.

Taxable supply date

The taxable supply date is activated in countries that require it.

Updated action names

The menu item action names have been revised to provide readable URLs.

## Localizations

Argentina 

Accounting:
Insert the legend "Operation subject to withholding" to invoices, credit notes, and debit notes PDFs of type A and M.

Inventory:
A printed delivery guide (Remitos) and PDF adaptations to cover the digital delivery guide (Remitos Digitales) have been added.

eCommerce:
Choose to show tax-excluded prices together with tax-included prices in eCommerce.

Australia 

Accounting:

- Peppol PINT is supported.

- All types of BAS are supported.

- New taxes are available by default, including the luxury car tax, fuel tax credits, wine equalisation tax, PAYG income tax, and fringe benefit tax.

- BAS report sections are rounded down to the nearest dollar by default.

- The company's GST registration status is reflected in legal documents.

Payroll:

- Single Touch Payroll (Phase 2) and SuperStream are supported via a clearing house integration.

- All security requirements mandated by the ATO have been implemented.

- The 2025-2026 fiscal year tax rules were updated as of July 1, 2025.

- Varied withholding tax rates are supported (e.g., cents per km, laundry, domestic and overseas travel allowances, overtime meal allowance).

- Payroll taxes (W1 to W5) are included in the BAS report closing.

- Import YTD balances for an employee across multiple income stream types (e.g., "working holiday makers" and "salary & wages").

- Backpay and salary sacrifice any payments on a payslip.

Bahrain 

Accounting:
The base localization package has been added: chart of accounts, taxes, and two tax reports: full VAT return and simplified VAT return (available from 17.0).

Belgium 

Accounting:

- A Belgian audit checklist is available.
- The annual statement is now available in XBRL format and is addressed to NBB.

- An integration has been added with CodaClean.

- Send Belgian VAT declaration to Intervat and MyMinFin via API.

- A non-deductible fiscal position is applied by default when creating a purchase receipt. This ensures that tax amounts on the receipt are treated as not deductible for the VAT declaration.

- A new activity type for the EC sales list and partner VAT listing has been added. An optional warning feature for users has been added and batch export from the "My Databases" page on odoo.com is supported.

Payroll:

- Automate multifunctional declarations (DMFA) and related changes through the secure file transfer protocol (SFTP) function directly in Odoo (available from 18.0).

- The Fiscal Voluntarism field can now be defined as a Euro amount, with a dedicated line in the salary rules.

- A new structure to manage salary advances has been added.
- The Dimona is now supported.

Brazil 

Accounting:

- Add shipping information into the NF-e DANFE and XML.

- Electronic invoicing for goods sold to end consumers is supported via Avalara Brasil, with related PoS adaptations (available from 18.0).

- The operation type can be changed on any invoice or sales order line.

- The CNAE (National Classification of Economic Activities) code has been added within the NCM so different codes can be used on the invoice lines.

- Tax exception rules are supported, allowing configuration of tax rate reductions, special benefits, and similar cases.

- Goods electronic invoicing has been added for vendor bills and purchase refunds (credit notes).
- 

- NFS-e (Services): Specify the place of service using the customer's delivery address.
- NF-e (Goods): Include the delivery address automatically when it differs from the customer's main address.

Point of Sale:
Send NFC-e in batch for PoS orders with Error AvaTax statuses. Export multiple NFC-e XML files from the PoS orders list view.

Inventory:
The CFOP field has been added to the operation types to provide more flexibility on customer invoices and vendor bills.

Bulgaria 

Accounting:
Sales and purchase ledgers are now downloadable from the tax report (available from 18.0).

Cambodia 

Accounting:
The base localization package has been added: chart of accounts, taxes, balance sheet, profit and loss report, T7 01 report, WT003 report, WT003 export, and generation of KHQR (available from 18.0).

Chile 

Accounting:
Add multiple cross-reference documents in a delivery guide and automatically add purchase order references from sales orders.

China 

Accounting:
The profit and loss and balance sheet reports have been updated (available from 18.0).

Colombia 

Accounting:

- The DIAN module supports the generation of AIU Service invoices, providing the ability to invoice for administration and incidental costs and the contractor's expected profit according to legal requirements.

- Support has been added for RADIAN events, including sending and receiving Reception Acknowledgements, Receipts, Acceptances, and Claims.

- The DIAN's certification process is handled automatically, with required accepted documents generated and sent without manual configuration.

Ecuador 

Accounting:

- The Sales and Subscriptions apps have been adapted to include the SRI Payment Method and automate the EDI flow from these apps.

- The EDI Dividends Withholding type of withholding can be recorded and sent to the government.

- Purchase withholdings are available for portal users. Generate a vendor bill by uploading the XML file of an invoice.

Egypt 

Accounting:
Expense accounts have been reworked and asset models have been added to improve user onboarding.

Payroll:

- The localization has been updated to include the calculations for annual leaves provision, sick leaves, unpaid leaves, remaining annual leave days compensation, out-of-contract days, and overtime.

- The tax exemption amounts and tax bracket percentages have been updated to match the Labor Law.

- Demo data has been added.

European Union VAT 

Accounting:
A new EU VAT territory country group has been created to exclude some territories of Europe that are not subject to VAT (Canary Islands, Antilles) to improve the standard behaviour when invoicing to those territories.

GCC countries

Accounting:
The Gulf Cooperation Council modules have been revamped. Add Arabic as a secondary language on accounting documents, including POS receipts. Country-specific layouts have been updated and aligned with standard flows.

Georgia 

Accounting: 
- The National Bank of Georgia has been added as a currency exchange rate provider.
- State names and codes for Georgia have been added in compliance with the ISO 3166-2 standard.

Germany 

Accounting: The structure of the German tax report has been improved.

Hong Kong 

Point of Sale:
A new payment terminal, QFPay, is available for Hong Kong.

Hungary 

Accounting:
Magyar Nemzeti Bank was added as a currency exchange rate provider. When sending a credit note to SPV that will fully revert an existing invoice, the credit note will be automatically sent as a Storno invoice.

India 

Accounting:

- GST compliance has been improved with enhanced GSTR-1 reporting: supply types are locked on posting and stored on journal items, invoices can be filtered for reconciliation, and the mandatory reports have been updated.

- Generate a detailed TDS report with a single click, streamlining the preparation and filing of TDS returns.

- The GST return process has been simplified.

- TDS section and rate are now automatically detected, PAN records for multiple contacts are centralized, and real-time multi-company alerts are provided for missing PANs or threshold/exemption violations.

- Add the MSME number and the MSME type on the PAN Entity record.

Payroll:
The ESIC report has been added.

Time Off:
Flexi Leave is now fully supported with Optional Holidays, ensuring employees can only select from eligible days when using this leave type.

Indonesia 

Accounting:
eFakture templates are CSV instead of XML format. eFaktur ranges have been removed.

Iraq 

Accounting:
The base localization package has been added: chart of accounts and taxes (available from 17.0).

Japan 

Accounting:
Import batch payments and bank statements via Zengin.

Jordan 

Accounting:

- An integration with JoFotara for e-invoicing has been added (available from 17.0).

- Non-Jordanian customer identification has been added to determine whether the customer is located in or outside Jordan (available from 17.0).

- A Demo mode has been introduced for internal validation testing (available from 17.0).

- Download the XML file for failed JoFotara submissions in developer mode.

- A restriction has been added to avoid deletion of the successfully submitted JoFotara PDF invoice. The error warning banner has also been improved.

- The tax report and taxes have been reworked.

- The import taxes are split into two taxes to account for the vendor base and customs VAT amount separately.

- Set the related invoice for an unlinked credit note for JoFotara submission.

- Support has been added for the Export and Development Area invoice types and the Cash payment method.

Payroll:
Calculations have been introduced for sick leaves, unused leave compensation, overtime, end of service, and related provisions. Demo data has been added.

Kazakhstan 

Accounting: The National Bank of Kazakhstan has been added as a currency exchange rate provider.

Kenya 

Point of Sale:
Point of Sale is now compatible with eTIMS in Kenya.

Korea 

Accounting:
Added taxes, the chart of accounts, and base accounting reports for the Republic of Korea, including profit and loss, balance sheet, and tax report (simplified and general taxpayer).

Lebanon 

Accounting:
The base localization package has been added: chart of accounts, taxes, and fiscal positions (available from 17.0).

Luxembourg 

Payroll:
Support for CIM credit has been added (available from 18.0). The Other Benefit in Kind field has been added, along with its associated salary rules.

Malaysia 

Accounting:

- Integration with Malaysia's LHDN MyInvois platform has been added (available from 17.0).

- Tax reporting has been improved. The existing SST-02 report now includes filters for custom and service codes, and the new SST-02A report is available.

- A QR code leading to MyInvois is embedded on invoice PDFs. Self-billing is now possible, including self-invoices, self-credit notes, and self-debit notes.

Point of Sale:
Submit e-invoices directly from PoS sessions. Generate, manage, and submit consolidated e-invoice for PoS orders not e-invoiced during the session (available from 18.0).

Mexico 

Accounting:

- The 2025 version of the DIOT report is available to all databases, including new columns and tax classifications (available from 16.0).

- Accounts are set by default on tax groups to simplify monthly tax closing.

- Select the IEPS tax breakdown per customer to optionally include it in the XML. All eight tax objects are now supported and can be assigned to individual invoice lines.

- A default account for credit notes and re-invoicing of old orders can now be selected for clearer accounting. Customs numbers are added into lots and invoice lines are split accordingly.

- The payment policy (PUE/PPD) is now selectable per invoice.

- Add fiscal complements into the CFDI XML directly by upgrading the Addendas module into Addendas & Complements.

- Add pro-forma (pre-invoice) functionality for previewing invoices before they are created.

- Payment complements receipts can now be printed directly on the invoice, regardless of whether the payment was applied during a bank reconciliation or as a direct payment.

Payroll:
CFDI generation is supported, allowing XML files for employees to be created and validated by the government.

Fleet:
Carta Porte vehicles are now handled directly from Fleet and the PDF has been revamped.

Netherlands 

Accounting:
It is now possible to generate a corrective settlement XML file.

New Zealand 

Accounting:
Peppol PINT is supported.

Oman 

Accounting:
 The base localization package has been added: chart of accounts, taxes, VAT return, and fiscal positions (available from 18.0). The import tax was split into two taxes to separately account for the vendor base and customs VAT amount (available from 18.0).

Pakistan 

Accounting:
Expense accounts have been reworked and asset models have been added to improve user onboarding.
Payroll:
Demo data has been added.

Peru 

Accounting: 
- Global and line-level discounts are now supported in UBL 2.1 XML electronic invoices, in line with the SUNAT guidelines.
- Support has been added for IGV Withholding (3%) on customer invoices. When an invoice includes this tax, the electronic invoice XML now automatically includes the required node with SUNAT code 62, allowing proper reconciliation with the customer's withholding document.
Point of Sale: SUNAT-compliant thermal printing has been added for Peruvian POS electronic invoices and receipts, generating legal electronic document representations (Factura/Boleta/Notas de Crédito) directly on 58/80 mm printers.

Philippines 

Accounting:

- Form 2550Q (Quarterly VAT Return) has been revamped to align with the latest BIR regulations.

- Improvements have been made to the SLSP, QAP, and SAWT reports.

- Reports can now be generated in the official BIR format.

- Direct export of .dat files is now supported for SLSP, QAP, and SAWT. The exported files are compatible with the latest release of BIR's Alphalist and ReLiEf modules.

- Generate disbursement vouchers that include a section for signature and check number for improved payment tracking.

Romania 

Accounting:

- eTransport declarations based on deliveries have been implemented (available from 17.0).

- A full synchronization with ANAF has been implemented.

- Invoices can be downloaded directly from the tax reports.
- Generate the D300 VAT report in XML format for submission to the tax authorities.

Saudi Arabia 

Accounting:

- Documents rejected by ZATCA with a 400 status code are handled correctly (available from 16.0).

- Documents with a 409 or 208 status code from ZATCA are handled as successfully sent (available from 16.0).

- The common name used in the certificate signing request during journal onboarding has been reviewed (available from 16.0).

- The PDF/A-3 format is supported for ZATCA PDF documents (available from 16.0).

- The Issue Date and Invoice Date are clearly distinguished in the invoice PDF (available from 16.0).

- Onboarding branches with ZATCA are supported (available from 17.0).

- The private key and API mode of a parent company are no longer inherited by its branches, improving flexibility and ensuring compliance with ZATCA requirements (available from 17.0).

- Deletion of invoice PDFs generated via Send & Print is now restricted to comply with ZATCA auditability rules (available from 18.0).

- The Certificate Signing Request generation for ZATCA now includes both the major and minor versions for improved compliance (available from 18.0).

- The VAT and Withholding returns were overhauled to use the new reporting engine.

- Chart of accounts, taxes, and tax groups were reviewed and reworked.

- Complete support for the gross and deducted withholding taxes was added.

- The ZATCA integration UX has been improved with clearer error messages, updated API validations, and backend handling of journal serial numbers. Sandbox and simulation documents can now be sent.

- Choose a ZATCA compliant reason when issuing a credit or debit note.

- Invoices affected due to a timeout in ZATCA are automatically added to the synchronous chain once the blocked invoice is processed.
- Less common taxes such as 15% PH PE HS, 0% Not Subject to VAT, 0% IT G, and 0% QT have been removed to prevent selection errors. The base tax grids have been updated for 0% PE and 0% PH taxes to improve reporting compliance.

Payroll:

- Loan management and advanced salary payroll structure have been added. New rules have been added for sick leaves, unused leave compensation, exit/re-entry, and other employee costs.

- Support for attendance-based contracts has been improved, and salary rule management has been streamlined.

- Demo data has been added.

Point of Sale:

- The ZATCA Phase 2 QR code has been added on receipts to ensure compliance and error handling has been improved for failure scenarios (available from 18.0).

- The refund issuing flows has been improved.

Singapore 

Accounting: 
- Peppol PINT is supported.
- GST taxes have been refined to align with current governmental requirements and to prepare for future GST InvoiceNow document compliance.

Slovenia 

Accounting:
Bank of Slovenia has been added as a supported currency provider. Support has been added for Slovenian Payment Communication Standard SI 01; this is automatically set as the default on newly created Sales journals in a company using the Slovenian localization (available from 18.0).

Spain 

Accounting:

- Modelo 140 Bizkaia and support for the SII cancellation workflow have been added (available from 16.0).

- The Libro Diario export was added to the general ledger and is now available via the journal audit report.

- The base localization package has been improved with additional default purchase accounts for common, everyday purchases, along with suggested default taxes.

- A new report dedicated to the Libro de IVA has been added.

- The Modelo 390 tax report was updated to the latest version published by the AEAT.

- Tax grids were updated for some taxes.

- The chart of accounts, taxes, and fiscal positions have been improved, including requirements specific to the Canary Islands.

Sri Lanka 

Accounting: A new localization package is available for Sri Lanka, including the chart of accounts, taxes, balance sheet, profit and loss reports, VAT 001 report, and WHT 001 report.

Switzerland 

Payroll:
The BFS are directly fetched from the postal code (available from 17.0).

Taiwan 

Accounting:
An integration with the ECPay platform has been added for the issuance and official government submission of Taiwanese e-invoices (available from 18.0).

Thailand 

Accounting:
The sales and purchase tax reports have been reworked. The chart of accounts has been updated to comply with TFRS for NPAEs, introducing detailed expense accounts, asset models, and VAT accounts. Relevant default accounts, taxes, and tax groups have been updated accordingly.

Türkiye 

Accounting:

- Support has been added for additional UNECE codes as Units of Measures for e-Fatura and e-Arşiv (available from 17.0).

- e-Fatura and e-Arşiv integration are now available via Nilvera (available from 17.0).

- Support has been added for currencies other than TRY for e-Fatura and e-Arşiv (available from 17.0).

- Deferred products can be used in subscription invoices, with support for e-Fatura and e-Arşiv (available from 17.0).

- Export e-Irsaliye XML from delivery orders and upload it to the Nilvera platform to generate GİB-compliant records (available from 17.0).

- The General Ledger can be exported in .csv format to generate the e-Ledger in Nilvera (available from 17.0).

- The total discount amount and exchange rate are displayed when sending electronic documents via Nilvera (available from 17.0).

- Documents sent via Nilvera now include additional company identifiers to ensure GIB compliance (available from 17.0).

- Invoice synchronization for the Nilvera integration has been improved.

- The Profit and Loss report has been improved to match the official format and the 700 series accounts are included for real-time financial reporting.

- The chart of account types has been updated and 7B accounts have been depecrated to ensure better GİB compliance.

- Verify the Nilvera status of multiple partners at once directly from the contacts list view.

- Pre-validation checks and other user experience improvements have been made to the Send wizard in the Nilvera integration for e-Fatura and e-Arşiv.
- The street fields have been combined into a single address line when generating the e-Fatura, e-Arşiv, and e-Irsaliye XML.
- Nilvera XML generation has been improved to display hyperlinks in notes cleanly, without reference brackets.
- e-Invoices with an Error status can be cancelled.
Payroll:

- Gross-to-net calculation has been improved to better fit market needs.

- Print certificates of employment.

- A new net-to-gross salary computation mechanism has been introduced.

- Demo data has been added.

Inventory:

- A warning has been added, and e-Dispatch generation is hidden if the delivery address isn't set on the delivery order. (available from 17.0).

- The Customs ZIP is now always applied when the delivery customer and address are outside Türkiye.

United Arab Emirates 

Accounting:

- The amount in the company's currency has been added to the invoice PDF for foreign currency invoices to comply with FTA Article 59 (available from 16.0).

- The VAT201 form has been completely overhauled to use the new reporting engine with updated taxes and tax groups.

- The corporate tax report has been refactored to make it more intuitive and support the use cases of being under the 375,000 AED threshold or having a net loss.

- The import tax calculation has been updated to account only for the customs VAT amount.

Payroll:

- Salary computation has been added for attendance and planning-based contracts (available from 18.0).

- Instant payment structure has been added for advanced salaries, penalties, and bonuses that are paid on an off-cycle basis.

- Unpaid leave deductions are calculated based on working days, excluding public holidays.

- Generate employee salary certificates.
- The WPS export has been enhanced to better align with MoHRE regulations.
- Calculations for annual leave and end of service provisions have been improved.

United States of America 

Payroll:
The 940 and 941 forms are supported. Support has been added for qualified overtime deduction rules under the "One Big Beautiful Bill," including a new salary rule and parameters to display capped overtime deduction information on employee payslips.

Uzbekistan 

Accounting: 
- The base localization package has been added, including a localized chart of accounts, taxes (VAT 12%, Export 0%, Exempt 0%), and standard financial reports: balance sheet and profit and loss.
- The Central Bank of Uzbekistan has been added as a currency exchange rate provider.

Vietnam 

Accounting:
The balance sheet and profit and loss report were added (available from 18.0).

## AI

Agents

Chat with AI agents that can learn from your documents and perform actions.

AI agent database queries

Internal users can now chat with an AI agent to query their own database records.

AI button in top bar

Ask AI for help using a button in the top bar.

AI fields

Use AI to fill in fields.

Ask AI search

Write a query in natural language and AI transforms it into an Odoo domain.

ChatGPT 5.0

ChatGPT 5.0 is now available.

Draft with Odoo AI

Use AI to write draft emails, improve text, or summarize the chatter.

Email template prompts

Insert an AI prompt in email templates and when sending individual or multiple emails at once.

Files for fields and actions

Ask AI to use the content of files when updating fields or performing server actions.

Gemini account

Use your own Gemini account as your Odoo AI provider.

Leads from AI agent

The Livechat AI agent can now generate leads.

Livechat integration

Connect an AI Agent to your livechat.

Server actions

Use AI to update fields in server actions.

Sources

Get answers based on your documents, Knowledge articles, website links, or PDF files.

Unified AI workflows

Configure default prompts and assign agents depending on where AI is called.

Voice transcript

Transcribe meetings or dictate text in real-time and get a summary.

Web page generation

Generate new web pages from a prompt.

Web search for fields and actions

Ask AI to search the web when updating fields or performing server actions.

## Appointments

Assign the same salesperson

If a salesperson handles a visitor, they will always be assigned to the randomly assigned appointments.

External website integration

Insert appointment calendars on external websites using iframes.

Flexible appointment scheduling

Easily switch from a weekly recurring schedule to a flexible schedule while configuring an appointment type.

Flexible appointments: duration display

The booking page now shows durations for flexible appointments.

Google Reserve integration

Allow customers to book appointments through your Google Maps page.

Group sessions

Organize and manage group sessions with control over user capacity and the ability to schedule multiple bookings per slot.

Reusable and default questions

Appointments are now more flexible and consistent, with reusable, configurable questions that make setting up and managing appointment types easier, while also simplifying reporting on answers.

Slot creation

Automatically generate slots at regular intervals based on the specified duration.

Unavailable resources

Users are now warned when attempting to book a user or resource marked as unavailable.

UX improvement

The interface has been simplified.

## Appraisals

Appraisal campaign

Create appraisals en masse directly from the Appraisals app.

Goals

Goals have been reworked, including the addition of templates and improved links to skills.

Goals for multiple employees

Assign goals to multiple employees.

Job target

Select a job target on an appraisal to assess an employees skill gaps.

## Attendances

Access rights

The new Officer access rights level provides access to all attendance records and reports for all employees without access to settings and configuration.

Automatic checkout

Automatic checkout and absence management are disabled for working schedules with flexible hours.

Disable GPS tracking

Disable GPS and IP tracking.

Location computation

Base check-in location on GPS coordinates when available.

Overtime rulesets

Create custom rules to define the extra hours of employees and their compensation.

Show presence indicator on kiosk

A presence indicator is now shown during manual employee selection on the kiosk.

Time off ledger

A time off ledger has been introduced to show employees attendances and time off by month, making it easy to spot absences without a time off request.

## Barcode

Lot and serial number properties

View and edit the properties of a lot or a serial number from the Barcode app.

Operation descriptions

Get instructions for receipts, internal transfers, and deliveries of products directly in the Barcode app.

Product source location

Manually set or update the location to source a product.

## Blog

Blog reordering

Reorder blogs in the list view to update their order on your website.

## Calendar

Calendar form view

The calendar's form view has been cleaned up and made more accessible.

WhatsApp reminders for calendar events

Send WhatsApp reminders for calendar events to attendees.

## Contacts

Address autocomplete widget

Check and fill addresses using Google Places API.

Contact autocomplete

When enriching contacts, the name and addresses of companies, if available, are automatically added using the user's language and/or alphabet (available from 17.0).

Contact form revamp

The contact form view and model have been reworked.

Contact statistics

See how many records are linked to a contact from the list view.

Contacts: "Mobile" field

The "Mobile" field has been removed from the Contacts model, and its value logged in the chatter.

Properties

Properties are now available on contacts.

## CRM

AI insights

Get AI-powered insights on the probability of winning your leads.

Business card scans

Generate leads by scanning business cards.

Auto-assignment to team leaders

Leads from external sources such as an email alias are automatically assigned to the sales team leader to ensure follow-up.

Lead assignment: priority filter

Set filters (e.g., languages) to prioritize matching leads during rule-based assignment.

Quick-create contacts

Link a new contact directly to a company in the new Kanban card when creating a lead.

## Dashboards

Blank dashboards

Create new dashboards by inserting data into a blank dashboard directly.

Charts: fullscreen display

Display charts in fullscreen in Dashboards.

Date filter

The date filter has been improved and new time periods have been added to support more detailed data analysis.

Favorites

Add dashboards to your Favorites.

Global filters

Manage global filters using the search bar.

Live Chat - Ongoing conversations

Monitor ongoing Live Chat conversations in a dashboard to spot staffing issues and adjust coverage by language or expertise as needed.

Measure sorting

Sort list and pivot measures.

Multicompany dashboards

It is now possible to set multiple companies on a dashboard.

Switch chart views

Switch views between bar, line, and pie charts.

Time-based charts

Time-based charts now support zooming and navigation. This option is enabled by default in full-screen Dashboard view and can be manually activated in Spreadsheets.

## Discuss

Audio and video device selection

Choose a microphone, speaker, and camera before joining a call.

Background blur for video calls

Enable background blur before joining a call.

Chat status

Set your status to Online, Away, Do Not Disturb, or Offline. When in Do Not Disturb mode, notifications and sound alerts are turned off, and incoming calls are declined.

Emoji shortcut

Add emojis to conversations using the : shortcut.

Live conference share audio

Share audio from your device while sharing your screen in a call.

Mention groups of users

Notify multiple people at once by @-mentioning roles in conversations.

Most recent emoji

Quickly add reactions using the shortcut bar with your favorite emojis.

Picture-in-picture mode

Use picture-in-picture mode in video calls to overlay a small movable version of the call while navigating other content.

Push notifications

Call invitations send push notifications.

Time off indication in avatar widget

Use the avatar widget to easily see if someone is off and their return date when assigning or contacting them.

## Documents

Accounting documents

The integration between Accounting and Documents has been improved and is now automated and enabled by default.

Add documents from chatter

Send an attachment from the chatter to My Drive.

Changes logged in chatter

The chatter now records changes, such as access right modifications, shares, and renaming.

Convert email into document

Emails with no attachments sent to an alias are now turned into a file.

Document location

Choose the desired folder when moving documents, creating shortcuts, duplicating files, and creating documents from attachements in the chatter.

Document rights warnings

Warning messages are displayed when general access rights are modified after a document move.

HR documents

The HR integration was reworked: the Recruitment subfolder has been removed, and multiple folders can now be created dynamically for each employee.

List view: quick access buttons

Share, download, rename, and get more details on documents in list view using quick access buttons.

Management with AI

Sort and trigger actions based on AI prompts.

New Journal Entry server action type

Create server actions to create journal entries, such as vendor bills and customer invoices from documents.

Preview and thumbnails

Preview and thumbnails support more file formats: .txt, .css, .json , .xml, .js, .html, .css, .md, ics.

Sharing and access rights

Manage the rights of multiple files or folders at once.

## eCommerce

/shop page layout and options

New layouts and options are available on the /shop page.

Abandoned cart email

Enabling abandoned cart emails only triggers emails for carts created after the feature is activated, not for existing abandoned carts.

Address selector

The address selector component displays addresses more clearly.

Alternative products block title

The Alternative Products block now includes a customizable title.

Attributes display

Filters on the shop page manage the pill display type. For Radio and Select types, a "View more" option appears when there are over 8 values, and a search option when there are over 20 values.

Cart page: list of supported payment methods

The cart page now displays the list of supported payment methods.

Cart summary

The mobile checkout experience displays an off-canvas cart summary.

Cash on delivery

The Cash on Delivery payment method can now be used with all delivery methods.

Catalog building block templates

New templates have been added for the Catalog building block to highlight selected eCommerce categories.

Category header options

New options are available for headers of catalog pages.

Checkout layout improvements

The checkout process for event tickets and appointments has been improved.

Checkout page

The pickup points' delivery addresses are no longer shown on the checkout page.

Click & Collect stock availability

A widget has been added on the product page to separately show the stock availability for delivery and Click & Collect.

Combo configurator

The combo configurator UI has been improved.

Comparison tool

The comparison tool design has been refined and is now entirely usable on mobile devices.

eCommerce URLs

Various improvements have been made to the /shop and /product URLs to improve SEO and prevent duplicate indexation.

Edit checkout stages

Edit the checkout steps, main buttons, and related labels.

Empty screens animation

Empty screens (cart, wishlist, and search) have a new animation.

Footer template with payment methods

A new footer template including the available payment methods has been added.

Free orders

Free orders redirect to the confirmation page instead of the portal page.

Google Merchant Center

- Products can be synchronized with the Google Merchant Center.

- Manage various feeds for the Google Merchant Center with improved performance.

Hover magnifier

The hover magnifier on product images has been replaced by the image lightbox.

Image attribute display type

Display product attribute values as larger images using the image attribute display type.

Improved SEO of product pages

The SEO of product pages was improved for Google's rich results.

Mobile cart summary review

The cart summary at checkout has been improved on mobile.

Onboarding steps

The creation of a new website now includes two extra steps to configure the shop and product pages.

Pagination

Pagination of the website has been improved to align more closely with Googles standards.

Pick up in store

- Selecting a store at checkout now shows unavailable products and allows you to save them in the wishlist or remove them.

- The "Pick up in store" delivery method is now better managed during checkout.

Prevent sales: product name on contact form

When customers click the Contact Us button enabled by the Prevent Sales of Zero-Priced Product feature, the name of the selected product variant is automatically added to the displayed contact form.

Product building blocks

The dynamic product building blocks are now more aligned with the main shop page.

Product configurator

The product configurator has been improved.

Product page

- New display options for product images, including carousels and grids, have been added to the product page.

- Choose between different layouts for the main action buttons and surrounding elements on a product page.

- The Share, Product List, and More Information options have been removed from product pages on eCommerce.

Quick reordering

Customers can now reorder products from a previous order directly from the Cart page or the portal.

Ribbons

Ribbons have been improved with dynamic assignment methods (e.g., New, On Sale, Out of Stock), a new layout, and sequence management.

Shipping address setting

The shipping address setting has been removed. Hiding or displaying a secondary address and delivery methods is now automatically determined by the product type.

Shop page: categories

Categories without products are hidden and new design options are available for displaying categories on the /shop page.

Subscription product page

The product page display has been improved for subscriptions.

Unit of measure selector

Extra packagings/units of measure added on a product are now selectable on eCommerce.

Variant preview on product cards

Attributes values and previews of variants can be shown on a product on the /shop page.

Wishlist page layout

The wishlist now supports layout options similar to those available on the main shop page.

## eLearning

Link-based course access

Restrict course access to people with the provided link.

## Email Marketing

New editor

The new editor has new building blocks and options to customize them.

## Employees

Birthdays

Employees can now use the new Birthday field to publicly display their birth date (excluding the year) on their profile. 

Employee and contract merge

Employees and contracts have been merged and a versioning mechanism has been introduced.

Learning management system basics

The basics of the learning management system have been implemented, with internal resume updates, certifications, and gathering of eLearnings, events, and external trainings in the same place.

Offers for non-users

Offers can be sent to employees that are not users.

Remote work enabled by default

Remote work is now enabled by default.

Skills and certifications

Skill management has been improved. Certifications have a dedicated menu item and their own tab on the Employee form.

Smart buttons

Employees can now access all smart buttons on their employee public profile.

## Equity

New app

The new Equity app helps fiduciaries and accounting firms report data pertaining to shares, shareholders, and beneficiaries by tracking option and share transactions as well as company valuation.

## ESG

Carbon analytics

Analyze emissions by year, scope, and activity type with visual graphics to identify impactful reduction areas.

Carbon footprint

Generate a complete carbon footprint report in tCO₂e by scope, aligned with GHG Protocol and Bilan Carbone standards.

Emission factors

Convert activity data into GHG emissions using physical (by quantity - e.g., kg COe₂/km) or monetary (by amount - e.g., kg COe₂/€) methods. Assign factors automatically based on product, partner, or account. Emission factors can be added manually or imported from certified databases.

IPCC database integration

Integration with the certified IPCC database is supported to complement the existing ADEME Source Database, enabling international market coverage and ensuring compliance with the GHG Protocol.

New app

Odoo makes ESG reporting simple, integrated, and affordable. The application integrates with Accounting, Employees, Fleet, Payroll, and more to automate data collection and make reporting as accurate as possible.

Social metrics

Track gender parity and pay gap using data from Employees and Payroll. View gender distribution by department, role, and location, and measure the pay gap between male and female employees.

## Events

Communication triggers

Schedule automated messages with two new communication triggers: "before the event ends" and "after the event starts".

Event cancellation

Cancel events. They can remain published online while all scheduled communications are stopped.

Frontend event ticket creation

Create event tickets alongside your events directly from the frontend.

Multi-slot events

Events can have multiple time slots, allowing attendees to register for specific sessions, e.g., for movie screenings.

Shared question library

Events now share a centralized library of questions, making it easier to define, reuse, and translate them.

Sponsors on event tickets

Only show specific sponsors on event tickets using the "Show on ticket" check box on the Event Sponsor form.

## Expenses

Disallowed expenses: reverse amount percentage

Disallowed Expenses now use an allowed percentage instead of a disallowed percentage" to account for potential >100% deductibility for expenses (which is now the case in Belgium). The Disallowed Expense Report now shows both amounts in those cases.

Expense reports removed

Expense reports have been removed to better align with common usage, where most reports contained only a single expense. Users can still submit, approve, and post multiple expenses at once from the list view by selecting several expenses at once. For employee-paid expenses, posting multiple expenses at once will generate a single bill per employee.

Partial bill deductibility

Configure purchase journals to register mixed expenses on a specific account (where part of the expense is made for private reasons and needs to be deducted from the company expenses). On purchase invoice lines, you can then change the Professional percentage at which that expense can be registered (the default is 100%).

Physical expense cards

Physical expense cards issued through Mastercard and Stripe Issuing are now supported. All transactions made with these cards are automatically synchronized and recorded, ensuring accurate, real-time expense tracking and simplified reconciliation.

## Field Service

Appointments

All appointment details are automatically included in field service tasks created upon appointment confirmation.

Geo-location tracking for tasks

Track worker location when running the timer.

Task report

Hide prices on the task report sent to customers.

Worksheet report: conditional visibility

Show fields conditionally in worksheet reports.

## Fleet

Odometers report

A report on the average mileage per month, based on odometer records, has been added.

## Helpdesk

Inactive tickets

Easily identify tickets that have been "rotting" (i.e., inactive for a long time) directly in the Kanban view.

Reimbursements: gift cards

Reimburse customers using a voucher code.

Send replacement products

Send customers a replacement for a lost, damaged, or returned item.

Ticket creation: files and images

Files and images from live chat conversations are correctly transferred to tickets created from the conversations.

Ticket dispatching based on tags

Assign tickets based on their tags.

## Inventory

Batches and dispatches

Batches and dispatches have been improved:

- Reorder deliveries from the map view to optimize your route.

- Set scheduled end dates to improve dispatch planning.

Forecasted report: expired products

The forecasted report shows which products with expiration dates should be removed from stock and excludes non-consumable items from available and forecasted quantities.

Inventory valuation

Inventory valuation has been simplified and new features have been added, including a new closing interface and support for transfer backdating.

Late Availability filter

Use the Late Availability filter on transfers and manufacturing orders to view sales orders with products expected to arrive after the delivery date.

Locations

The location configuration has been simplified and unnecessary virtual locations created by default have been removed.

Lots and serial numbers

- Navigation from customer serial numbers and lots has been improved to provide direct access to the related information, and the Lot/Serial Number form view has been reworked.

- Define product-specific lot and serial numbers.

Master Production Scheduler

- Calculate the forecasted demand for future periods using historical data or actual demand in the MPS.

- The Maximum to Replenish field in the Master Production Scheduler has been removed.

- 
The Master Production Schedule has been improved to:
- separate direct and indirect demands;

- easily identify situations where actual demand exceeds forecasts, using the Forecast Too Low filter;

- handle early arrivals or production completion more accurately when actual replenishment exceeds the suggested amount, forecasted quantities are adjusted accordingly.

Merge batches or waves

Merge batches or waves with the same operation type.

Merge packaging with UoM

Product units and product packagings are merged into one simplified model. UoM categories have been removed.

Multiple routes on sales order lines

Set multiple routes combined on a sales order line (i.e., MTO and Buy) to decide how to replenish the product for that sales order.

Packages within packages

Create packages that contain other packages.

Partner in traceability report

Vendor and customer names are displayed directly in the traceability report.

Physical inventory

The Physical inventory view and its related features have been simplified and improved.

Product route configuration

Product routes are set automatically when possible (i.e., Buy route for purchase products and Manufacture route for products with bills of materials).

Products with missing vendors

When a vendor is missing for a product with an MTO route, the default warehouse route is now used to prevent blocking salespeople.

Products' quantity on hand

Update a product's quantity on hand using a dedicated field on the product form.

Reordering rules

- Reordering rules now include a horizon setting (set to 365 days by default), a new deadline field showing the latest date to reorder a product before hitting minimum stock, and data previews (e.g. order frequency, average stock) based on past demand and chosen min/max values.

- Reordering rules now use the unit defined on the vendor pricelist or on the bill of material as the default multiple to calculate the quantity to order. The quantity to order can now exceed the maximum quantity when using a multiple to avoid cases where not enough quantity would be ordered due to downward rounding.

Replenish on Order (MTO) route

Activate the Replenish on Order (MTO) route using a new setting.

Replenishment view

The replenishment view now displays default values in previously empty columns (e.g., Route, Vendor, etc.). This allows to filter by vendor to quickly identify rules that already use a specific vendor by default and rules that can be changed to use that vendor to fulfill an order.

Report improvements

The Picking Operation and Delivery Slip reports have been improved.

Reservation upon adjustment validation

The reservation process is triggered immediately after adjusting inventory to identify the next processable item.

Suggested quantity to replenish in vendor catalog

Create purchase orders using suggested quantities to purchase from your vendors, based on sales and demand history of your products.

Update quantity

It is possible to update the product quantity on hand directly from the form view of the product.

UPS Connector signature required

Configure UPS integration in order to request a signature from the customer.

Warehouse in traceability report

The warehouse short code is displayed in the traceability report.

WhatsApp shipping notifications

Send shipping notifications through WhatsApp.

## Knowledge

Collapsible toggles

Hide text under collapsible toggles using the "Toggle list" powerbox command.

Custom templates

Create custom article templates from existing articles.

Public view

Public and portal users can search through the content of articles.

## Live Chat

Access rights

Live chat access rights have been updated: Live chat users can view conversations from other agents. Only live chat admins can configure channels and chatbots.

Call measures

View call statistics from Live Chat conversations such as calls made, sessions with calls, and call duration.

Chatbot session statistics

View statistics on chatbot performance: which bot handled conversations, visitor selections, forwarded expertise, and peak conversation days.

Concurrent chat limit

Limit the number of conversations a live chat operator can handle at the same time.

Conversation copy

Send a copy of a previous conversation to visitors.

Create lead and forward

Use the chatbot to create a lead and forward the conversation to the right person.

Created tickets/lead measures

View statistics on tickets or leads created from Live Chat.

Forward chats based on expertise

Forward live chats to operators with the appropriate expertise using the chatbot.

Handled by bot/agent measures

Compare chatbot and agent conversations in reports.

Info side panel

See visitor details and conversation info. Add notes and set a status to quickly spot which conversations need your attention.

Info side panel: tags

Tag live chat conversations to categorize them and get statistics.

Redirect after positive ratings

Redirect visitors to an external link after they submit a positive rating.

Success statistics

Analyze live chat conversations that the chatbot couldn't forward, were escalated, or were abandoned before a response.

## Maintenance

Email alias

The email alias field has been moved from the equipment category to the maintenance team.

## Manufacturing

Bills of materials: default batch size

Set a default batch size on bills of materials to preset the quantity on new manufacturing orders.

Bills of materials: Highlight consumption field 

Manual consumption has been removed from the bill of materials configuration. 

BoM overview and work center capacity

The bill of materials overview has been updated and the work center capacities have been simplified.

Gantt view for manufacturing orders

A Gantt view has been added for manufacturing orders, making it easier to visualize ongoing and upcoming manufacturing orders.

Manufacturing order UX

An editable MO deadline field has been added on the manufacturing order form. The Consumed checkbox column has been removed. The list icon on move lines has been replaced by a clickable "Pick" link for all stock moves.

Multiple serial/lot numbers per MO

Generate multiple serial or lot numbers from a single manufacturing order.

Operation costing

Determine how manufacturing operations are valued: based on real usage or at a fixed cost.

Split manufacturing order

Splitting manufacturing orders is more user-friendly.

Subcontracting reception

All standard inventory reception features are also available for subcontracting.

Work center employee cost impact

For AVCO and FIFO valuations, labor on manufacturing orders uses the hourly employee cost set on the work center if no hourly cost is set on the specific employee record. This cost directly impacts the calculated valuation of the finished product.

Work order search view

Filter work orders by attribute values or component and edit the start date from the list view to better organize the sequence of work orders to process.

Work order status

The work order status is editable.

## Membership

Removal/replacement

The Membership app was replaced with a more integrated Partnership module, allowing users to assign grades and pricelists to members/partners.

## Online Payments

Automatic creation of payment providers

All payment providers are automatically created in new companies to simplify online payment setup.

DPO

DPO is available as a payment provider for the African market.

ECPay

An integration has been added with ECPay, an online payment provider for Taiwan.

Iyzico

Iyzico is available as a payment provider for the Türkiye market.

Lazada

A marketplace integration with Lazada has been implemented to fetch orders and delivery slips, and to synchronize inventory.

Mercado Pago

Mercado Pago now supports paying through installments and tokenizing payment methods for recurring payments.

Mollie

The payment method Trustly is now available with Mollie.

Nuvei

Nuvei is available as a payment provider for the LATAM area (available from 18.0).

Paymob

Odoo now supports the Paymob payment provider, available in Pakistan, Egypt, Saudi Arabia, the United Arab Emirates, and Oman.

Razorpay

The FPX and PayNow payment methods are available with Razorpay.

Redsys

The Redsys payment provider is now supported, available in Spain.

SEPA payment token

Confirming a SEPA mandate from the backend creates a payment token that customers can use for online payments.

Stripe

Stripe now supports Amazon Pay.

Stripe supports Twint

Stripe supports Twint as a payment method.

Test versus live transactions

It is now possible to distinguish live from test payment transactions in reporting.

## Payroll

Contract end date on offers

Set both a start date and an end date when creating an offer for a contract.

Contract source

Choose if the working entries will consider extra hours from Attendance records.

Manual file generation in reports

Individual file generation is a manual operation to grant more flexibility.

Master report

A payroll master report is available for all localizations.

Multiple accounts for employees

Allow an employee to have multiple bank accounts and split their net salary across them.

Multiple analytic accounts

A full analytic distribution is available on employees to allow splitting their costs across multiple analytic accounts.

Pay runs

Batches have been renamed to Pay Runs. Create and process pay runs using the new UI/UX with guided steps.

Payslip correction

A correction workflow is applied to faulty payslips when using the Revert action.

Payslip generation

Unapproved leaves no longer prevent the generation of payslips.

Payslip lines report

A new report on payslip lines has been added.

Properties as benefits

Benefits in the salary configurator can now be linked to salary inputs (properties), allowing fully customized flows.

Properties on employee records

Add properties on employee records related to salary rules to simplify payroll management.

Salary attachments

Salary Attachments are now managed directly within Salary Adjustments.

Salary rule parameters

The salary rule parameters' list view has been improved.

Salary rules domain condition

Define domain-based conditions directly on salary rules.

Work entries

The Work entries widget has been reworked to support easier manual creation.

Work entries duration

Work entries are now composed of a duration and a date instead of a start and end datetime.

Work entry type: rate

Define a specific rate on a work entry type for paid time.

## Phone

Call form view

Use the call form view to see call details, access all records linked to the number (opportunities, subscriptions, tickets, etc.), and view transcripts or recordings when available.

Call recording

Call recording can be enforced, controlled manually by users, or disabled entirely.

Call transfer

New call transfer options have been added, including: ask before transferring, transfer directly, or get the callback if theres no answer.

UX/UI improvements

The UX/UI has been improved and new features have been added, including "Do Not Disturb", an advanced keypad with smart search, and quick access to applicant/contact info.

## Planning

Auto-plan on flexible schedules

Auto-plan shifts for employees on flexible schedules.

Multi-day shift creation

Create shifts for multiple days at once in the Gantt view.

Multiple shift creation from calendar view

Create multiple shifts at once from the calendar view using a shift template.

Planning / Attendance analysis

Compare planned hours to attended hours.

Planning preview

Preview an employee's planning before publishing it.

Rental orders

Create rental orders for shifts. Checks are implemented to prevent confirmation when no resources are available and the orders rental period and shift dates are automatically synchronized.

Schedule printing

Print your employees' schedules.

## Point of Sale

Default ZPL formats

Four default ZPL formats are available.

Global invoice

Generate a global invoice for a given customer.

GoFood delivery integration

Support has been added for GoFood orders and menu sync for Indonesia and Vietnam.

GrabFood delivery integration

Support has been added for GrabFood orders and menu sync for Cambodia, Indonesia, Malaysia, Myanmar, Philippines, Singapore, Thailand, and Vietnam.

Group products

Group products by parent and child categories in the point of sale terminal.

Long press/click for product info

To display the product information from the point of sale, long press/click the product card.

Minimal rights profile

A third point of sale employee rights profile is available for employees that do not require more than the ability to go through simple checkouts.

One-click payment validation

Validate payments with a single click.

PoS presets

Create predefined presets to quickly apply order schedules and modes such as deliveries, pick-ups, and more.

Product info

Product information is now accessible via the action button.

Restaurant: allergen management

Manage allergens for self-orders.

Restaurant: organizing orders into courses

Organize orders into courses to ensure starters, mains, and desserts are sent in the correct order.

Restaurant: preparation time report

A built-in report has been added for the preparation time of orders.

Settling due accounts

Select the customer's due accounts to be settled.

## Project

Add assignees via email

Add assignees in the "To" field when creating a task via email.

Auto-plan on flexible schedules

Auto-plan tasks for employees on flexible schedules.

Drag and drop tasks in calendar view

Schedule existing tasks by dragging and dropping them in the calendar view.

Gantt view for tasks on portal

Tasks can now be viewed in a Gantt view from the portal.

Multiple priority levels for tasks

Set more granular task priorities with multiple priority levels.

Project templates

Create projects using templates with pre-filled details. Assign roles to tasks in project templates and select the users to fill those roles when creating a new project.

Share private projects with portal users

Grant portal users access to private projects.

Task scheduling based on template

When creating a new project from a template, plan all tasks according to the template.

Task template for service products

Select a task template for service products configured to automatically create a task when the sales order is confirmed.

Task templates

Create reusable task templates to speed up task setup and standardize workflows.

## Purchase

Alternative RFQs

Create multiple alternative RFQs at once using the correct vendor currency and copied analytic distribution from the original RFQ.

Cancel and delete purchase orders

It is now possible to cancel and delete purchase orders in order to allow the creation of fake orders during testing.

Forecast-based purchasing

Purchase required quantities from the product catalog based on forecasted demand for upcoming days.

Group RFQs for vendors

Define for each vendor whether RFQs should be grouped based on the expected arrival date.

Improved RFQ dashboard and UX

The UX and the Request for Quotation dashboard have been improved.

Purchase and product units

When purchasing in a unit or packaging that differs from the product unit, the cost in the product unit is displayed beside the cost in the purchase unit in the product catalog.

Purchase catalog

The Purchase catalog is now more dynamic: it displays suggested quantities on product cards and allows to view forecasted quantities for a specific date range.

Purchase orders from sales orders

Create purchase orders from sales orders coming from another Odoo database using a dedicated button on the customer portal.

Remove locked status

The "Done" status of purchase orders has been replaced by a boolean field to lock/unlock a purchase order.

Unit price smart update

The unit price of order lines will not recompute automatically after being edited manually.

Upload bill

The purchase team can directly upload a vendor bill from the purchase order regardless of the control policy of the products.

## Quality

Quality control points: failure location

Select a failure location for any quality control point, regardless of its type.

## Recruitment

Campaign tracking

Track applicants by campaign.

Job matching

The skills redesign includes a job position matching gauge chart that reflects how suitable the applicant is for the role based on their skills and degrees.

Salary configurator states

States in the personal info section of the salary configurator are related to selected country (available from 18.0).

Talent pool

The candidate system has been replaced by a talent pool system.

## Referrals

Access rights

A dedicated access rights section has been added for the Referrals app.

## Rental

Drag and drop

Drag and drop rental reservations in the scheduled rentals Gantt view.

Hourly booking

The handling of overnight rental products like hotel rooms has been improved. Define the pickup and return times on the schedules rental Gantt view or form view.

## Repairs

Repair orders from inventory operations

Create a repair order from any individual inventory operation instead of from an operation type.

## Sales

Amazon Connector

The Amazon Connector supports the new Irish marketplace.

Catalog sections

Create and manage order sections in the catalog. Use tags to search for products.

Combo products improvements

Easily modify combo quantities and choices from the backend.

Commission adjustment

To adjust the achievement of a salesperson, select a salesperson on the commission plan for which the amount is added and a salesperson for which the amount is reduced.

Commission forecast notes

It is now possible for salespeople to add notes about their commission forecast for a period in the list of commissions.

Delivery date based on product type

The expected delivery date of a sales order now only takes into account Goods products and not Service products.

Editable optional products in portal

Sections or subsections of quotations can now be set as optional, with portal users able to select quantities for those lines directly in the portal.

Gelato integration

Connect Odoo with Gelato, a print-on-demand service (available from 18.0).

Hide section prices and composition

Hide prices of lines in (sub)sections to reveal only the total or hide all the lines of a (sub)section to reveal only the total in the report and portal.

Loyalty cards: product domain

Define a product domain when setting conditional rules for loyalty cards.

Partial payments on user portal

Partial payments for customer invoices are now handled in the customer portal, allowing down payments or partial payments according to payment terms and enabling full payment when desired.

Quote builder: default headers/footers

Set default headers and footers for the quote builder.

Sales team notifications for paid invoices

Sales teams are notified when invoices have been paid.

Separate print and send quote

A button has been added to print and mark a quotation as sent without sending by email.

Shopee integration

Fetch orders and delivery slips, and synchronize inventory levels with the Shopee integration (available from 18.0).

Wallet top-up from portal

Users can now top up their e-wallet from the portal.

## Shop Floor

Closing manufacturing orders

Determine whether manufacturing orders can be closed directly from Shop Floor.

Component moves

Component consumption in Shop Floor is fully compatible with barcode scanning and available barcode commands.

Design update

The Shop Floor app has undergone a comprehensive design update.

Modify work order routing

The "Modify Routing" button on work orders provides the options to move the order to a different work center or to add a new work order.

## Sign

Assign fixed signers on templates

Templates now allow assigning a specific contact as a signer.

Auto-complete

Fields can now be auto-completed from any other text field in Odoo through the field settings.

Automatic signer assignation

When sending a signature request from a record, the related customer (or the relevant party) is automatically added as the signer.

Certificate of completion reference

Use digital certificates to sign your documents. The reference of the certificate of completion has been added to the signed document.

Copying and pasting fields

Copy, paste, and duplicate fields on any Sign document, including with keyboard shortcuts.

Document envelopes

Upload multiple documents to be signed at once and send them as a single sign request.

Expiration date for shared links

Set an expiration date on shared links.

Import files from Documents

Import files from the Documents app to the Sign app.

List of remaining documents to sign for non-portal signers

Signers without a portal account can see their remaining documents to sign immediately after completing a signature.

Quick sign

Sign and download documents more quickly when they only contain autocompleted fields (e.g., signature, email, etc.).

Read-only fields

Sign fields can be set to read-only.

Revamped user interface

All aspects of the Sign app have been revamped for a better and more intuitive user experience.

Select multiple fields to move them all together

Select and move multiple sign items at once by drawing a selection area with the mouse.

Send via WhatsApp

Send sign requests via WhatsApp.

Sign documents in the chatter

Any PDF in an app's chatter can now be opened in Sign to request signatures, or sign documents yourself.

Signature request: upload PDF

Upload a new PDF when sending signature requests from a record instead of having to select a template.

Template layout preview

Preview templates' layouts by clicking on the Preview button.

Update template documents

Sign templates now allow updating the underlying document without recreating the template. Existing signature fields are automatically preserved in their original positions.

## Spreadsheet

Access to referenced records

"See record" access rights are automatically granted when a record is referenced in another cell.

Chart and axis titles: font size

Edit the font size of the chart and axis titles.

Chart customization

Use and customize charts inserted from other Odoo apps.

Command palette

Open the command palette in Spreadsheet using the shortcut CTRL + K.

Complex formulas auto-format

Complex formulas are automatically formatted to improve readability by splitting and indenting sub-formulas onto separate lines.

Conditional formatting using custom formulas

Use custom formulas to define conditional formatting.

CSV files

Open CSV files in Spreadsheet.

Data validation conditions: auto-complete

Formula-based data validation conditions offer auto-complete suggestions.

Data validation import/export

Import and export data validations from/to XLSX files.

Date filters and additional time periods

The date filter has been improved and new time periods have been added to support more detailed data analysis.

Dynamic pivot tables

Dynamic pivot tables now support cross-model drilling.

Dynamic pivots: dimension grouping

Group dimensions in dynamic pivots.

Filter on boolean

Use global filters on boolean fields.

Formula: arguments

Use the F2 key in the formula composer to toggle between selecting and editing in arguments.

Global filters

- Global filter suggestions are automatically generated based on spreadsheets' data sources.

- View and edit matching global filters per data source.

- Use operators in global filters to get more detailed results.

- Add numeric global filters.

- Add multiple values in text global filters.

Irregularity map

An irregularity map feature has been added to analyze spreadsheet formulas for patterns and highlight inconsistencies.

Middle click

Use CTRL + click or middle click in Spreadsheet to open links in a new tab.

New chart types

- Use funnel charts to display data that progressively decreases over stages of a process.

- Use geo charts to display geographical data, including data from Odoo.

- Use radar charts to compare multiple datasets.

- Use sunburst charts to display hierarchical data.

- 
Use tree map charts to display hierarchical/categorical data.

New functions

- The TEXTAFTER, TEXTBEFORE, SUBTOTAL, REGEXEXTRACT, and VALUE functions have been added.

- Use the ODOO.BALANCE.TAG formula to retrieve the balance of a list of accounts.

- Use the SWITCH function to evaluate an expression against a list of values and return a corresponding result for the first matching value.

Number filtering based on condition

Filter numbers based on a condition.

Odoo pivot table insertion

Insert an Odoo pivot table from a spreadsheet.

Offset periods

When filtering by date, it is now possible to define offsets of greater than 2 periods.

Pivots

- Collapse rows and columns in dynamic pivots when data is grouped by at least two dimensions.

- Sort pivot values by measure.

Presorted fields by type

Fields are presorted by type to simplify global filter creation.

Reordering data series in charts

Reorder data series in the Chart panel.

Side panels

Pin side panels and display up to two of them at the same time.

Spreadsheet on mobile devices

Usability and readability of spreadsheets on mobile has been significantly improved.

Support "+" as first character for numbers & formulas

Start with "=" or "+" to type a formula in a cell.

## Studio

Adding followers dynamically

Add followers dynamically in automated and server actions.

Button tooltips

Add tooltips on buttons with Studio.

Fixed column width

Define a fixed column width in the list view.

HTML actions

HTML fields can be updated in actions and automations.

Kanban fold field

In Kanban customization of custom models, select a boolean field from the group that indicates if the column should be folded.

Record duplication

Define in Studio whether records can be duplicated.

Report editor expressions

In the report editor, easily move, copy, and paste expressions.

Report: field edition

Edit any field added on the report.

Server actions: relational fields

Define relational fields in server actions using a widget.

Typeahead search

Configure searches to trigger after X characters on M2O and M2M tag fields.

View editing

Open Studio from any view, even if the view was accessed by browsing relations.

## Subscriptions

Automation rules

The Subscriptions app's dependency on automation rules has been removed. Those who need it can install Studio to use automations everywhere.

One-time sale of goods

Allow subscription goods to be sold as one-time sales without generating a recurring plan.

Pricelists for recurring products

Define price rules including discounts or formulas for recurring prices.

Prorated product prices

Recurring product prices can now be prorated, allowing adjustments for upsells, calendar alignment, etc.

Subscriptions based on delivered quantity

Subscriptions based on the delivered quantity can now be invoiced immediately, without waiting until the end of the invoicing period.

Update invoicing address from portal

Subscription customers can now update the invoicing address linked to their subscription.

## Surveys

Lead qualification

Generate leads when survey participants pick specific answers.

Result analysis

Visualize survey results in Spreadsheet, either from the survey or by using the ODOO.SURVEY function.

## Time Off

Complex durations

It is now possible to create a single time off request with a non-uniform duration such as 1.5 days or 3 days and 6 hours.

Halfday visibility

Half days are clearly shown in the calendar view using a distinct visual.

Simultaneous leaves

It is now possible to schedule one leave at the same time as another to manage cases such as an illness during a training and tracking remote work.

Time off type

The Country field has been added for time off types.

UX improvements

The UX of the Time Off app has been improved.

## Timesheets

Multiple timesheet creation from calendar view

Create multiple timesheets at once from the calendar view.

## To-do

Deadlines

Set deadlines for your to-dos.

To-do email alias

Create to-dos by emailing an alias.

## Website

404 page

The 404 page's UX has been improved to avoid confusion.

Alerts design

The design of alert snippets has been improved.

Building blocks

- New and modern building block templates are available in the website editor.

- Some building blocks with multiple templates have been divided into multiple separate building blocks.

- The readability of dynamic building blocks in the dialog building blocks selection has been improved.

Building blocks: events and blogs

The Events and Blogs building block categories offer multiple template options, including ones that highlight a single event or blog post.

Customization of portal pages

Customize the login, signup, and password reset portal pages.

Event sidebar

Choose which blocks to hide or reveal in the event sidebar.

Forms: field validation

Add field validation criteria for individual fields in website forms to ensure correct input from visitors.

Grid layout: inner content blocks

Drop inner content blocks anywhere within a grid layout section.

Hide headers/footers

Hide the header and/or footer on pages (e.g., event, blog).

Mobile: mega menus

The mega menu is displayed on mobile devices using multiple nested levels.

Navbar alignment

Select specific navbar alignments for desktop versus mobile.

New parallax scrolling effects

The "Zoom in" and "Zoom out" options are now available for the parallax effect.

Scrolling mode for carousel snippets

The Scrolling Mode field determines if snippets should move all together or one by one when multiple elements are displayed.

SEO improvements and checks

Implement best SEO practices for your records: add keywords, prepare the schema markup, and check your content to optimize SEO.

Settings

Website's settings have been simplified and reorganized.

User profile page

The user profile editing view has been improved with support for uploading a cover image.

Website builder UI

The website builder UI has been simplified to improve the onboarding for new users.

## WhatsApp

WhatsApp account in conversations

The WhatsApp account used to reply is displayed in conversations.

### Odoo Experience on YouTube

 1. Use the live chat to ask your questions.

 2. The operator answers within a few minutes.

 Watch now
