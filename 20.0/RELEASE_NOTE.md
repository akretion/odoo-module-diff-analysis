# Odoo 20

## Release Notes
September 2026

## Technical

Mail: in-body tracking

Tracking values have been removed; the tracking message is now generated on the fly. All features linked to tracking values (burndown charts, stage duration, etc.) have been updated for this new framework. Admins who still need tracking values can install a dedicated module.

Many-to-one field improvements

Many-to-one fields are now synced with the backend, and adding/removing values has been improved, even with large lists.

Push notifications

Push notifications are no longer handled by Firebase but rather by in-house Odoo push tools.

Track source of postings

Mail.mail records now specify the source of their content to ease auditing.

Tracking user group changes

User group changes are now logged in the user's chatter as well as in the console logs.

## Industries

Accounting Firm

The Accounting Firm industry package has been updated to better manage identity documents and anti-money laundering and to include the Database app, providing an integrated dashboard for handling databases for customers (available from 19.0).

Agri-Equipment Rental

A new industry package is available for managing rental and maintenance operations for tractors, harvesters, and other agricultural machinery.

Beauty Parlor

A new industry package is available for beauty parlors, including customer management, appointments, treatment follow-up, and invoicing (available from 19.0).

Catering

A new industry package is available for catering services, managing the entire business flow from client contact to on-site planning.

Community Care

A new industry package is available for Community Care organizations managing staff holidays and planning, residents with their related data and calendars, and knowledge transfer.

Construction Builder

The Construction Builder industry has been renamed General Contractor and now uses section templates to manage a catalog of frequent work in order to quote efficiently.

Construction Developer

The Construction Developer industry has been renamed Property Developer and has been overhauled to replace the work item structure with standard Odoo Manufacturing and Odoo Inventory features, such as bills of material, manufacturing orders, standard routing, and locations. 

New features include:
- cost-based quoting with a dedicated cost update widget
- a work breakdown structure (WBS) view from sales orders and projects for easier scheduling
- dedicated worksite stock locations for inventory tracking, on-site delivery, and drop-shipping
- a progress management view displaying completion that increments dynamically
- customizable dashboard reporting and sharing of remarks on project tasks with suppliers

Custom Industrial Equipment

A new industry package is available for engineering-to-order solutions designed to meet the complex challenges of engineering, procurement, and construction (EPC) and turnkey projects. The end-to-end process covers everything from initial design and prototyping to manufacturing, procurement, and on-site installation, as well as long-term operational success through dedicated maintenance and technical support.

Deposit module

The Deposit Management module (used in the Beverage Distributor and Micro-Brewery industries) is now available on its own.

Electronic Refurbishment

A new industry package is available for electronic device refurbishing, including handling of refurbishment grades and VAT margin scheme accounting.

Excise module

The Excise module (used in the Micro Brewery, Beverage Distributor, and Vineyard industries) now distinguishes production and inventory adjustment in the excise report.

Hospitality industries

The Hotel, Holiday House, Guest House, and Campsite industries have been updated:

- Modules have been added for managing housekeeping and for balancing customer bills from the bar and restaurant on their final stay invoice.

- The simplified rental price list management is leveraged to enable seasonal pricing.

- A "Steering" menu and dedicated dashboard have been added to monitor occupancy and availability (available from 19.0).

- Early check-in and late check-out are now supported.

- A dedicated Odoo dashboard is now available in the Dashboards app.

Hotel

The Hotel industry package has been updated to include the guest count and support multiple companies and branches to run multiple accommodation businesses in parallel.

Interior Design

A new industry package is available for interior design companies, including website inquiries, resource planning, procurement, project management, and profitability tracking.

Machine and Tool Rental

A new industry package is available for machine and tool rental business managing rental contracts, quarantine on returns, consolidated down payments based on consumption, machine run-time monitoring, and maintenance planning (available from 19.0).

Mental Therapy

A new industry package is available for mental health professionals, managing patients, therapy sessions, session notes, and invoicing (available from 19.0).

Nonprofit Organization

Donation receipts have been improved, and the ability to organize petitions has been removed.

Pet Groomer

A new industry package is available for pet grooming services (available from 19.0).

Physical Therapy

A new industry package is available for physical therapists, managing patient care, therapy sessions, treatment tracking, and billing (available from 19.0).

Property Management

The Property Owner Association and Real Estate industries can now be installed on the same database as the Property Management industry.

Property Owner Association

The Property Owner Association industry has been updated:

- Owner history tracking, general meeting animation and reporting, and cost distribution based on meter readings and periods are now supported (available from 19.0).

- The Property Management industry can now be used in the same database as the Property Owner Association industry.

Public Institution

A new industry package is available for public institutions, including managing citizen support, appointments, citizen portal, facility rental, community events, and more.

Real Estate

The Real Estate industry has been updated:

- Matchmaking criteria can be adapted and updated in the matchmaking form (available from 19.0).

- The Property Management industry can now be installed on the same database as the Real Estate industry.

Talent Acquisition

Integration between applicants, talent pool, and job positions has been improved, with a more user-friendly interface, better synchronization between talent and applications, and more accurate talent-to-job matching.

Vineyard

A new industry package is available for vineyards, managing the entire wine-making flow from grape to wine.

## Accounting

Accounting Firms mode settings

The "Accounting Firms mode" settings have been updated to increase their flexibility.

AI-powered financial insights

Ask the AI assistant for insights into your company's finances based solely on the accounting data in your database.

Analytic distribution for write-offs

Define an analytic distribution when creating a write-off during reconciliation.

Annual report layout

Annual reports now use the company's document layout.

Asset depreciation

Rates and float numbers can now be used when depreciating assets.

Bank consistency

- All journal entries impacting bank accounts must now originate from bank transactions, preventing manual journal mismatches.
- 
Statement validation feedback has also been refined to quickly identify and fix balance inconsistencies.

Bank reconciliation summary report

The bank reconciliation summary report provides a detailed view of reconciled and unreconciled transactions based on the end date of the selected period.

Bank synchronization: Syncfy

Syncfy is supported as a third-party provider for bank synchronization in Latin America.

Bill line prediction

When encoding a bill (manually or via import), Odoo suggests line elements based on the bill history and the label. The product, account, tax, analytic distribution, and the vehicle will be automatically pre-encoded based on the best matches. Manually encoded values on existing lines are still preserved.

Bill options display

Options added on a bill such as depreciation models, vehicles, and deferred payments are stacked vertically under the account instead of being displayed horizontally.

Bill/invoice matching rules

Configure "Matching Rules" to set payment tolerance and matching order for automatic bill/invoice reconciliation.

Cash journal entry hashing

Secure cash journals with entry hashing upon reconciliation.

Conversion rates

Conversion rates are now clearly displayed when registering payments in a foreign currency.

Cumulative Translation Adjustment (CTA)

An auditable "Cumulative Translation Adjustment (CTA)" line has been added to the balance sheet, trial balance, and general ledger to automatically balance discrepancies caused by different exchange rate types (e.g., historical vs. closing) across account categories.

Currency exchange rate providers

New currency exchange rate providers are available:
- The Central Bank of Azerbaijan (available from 19.0)
- The National Bank of Georgia (available from 19.0)
- The National Bank of Kazakhstan (available from 19.0)
- The Saudi Central Bank
- 
The Central Bank of Uzbekistan (available from 19.0)

Customer invoice reminders

Send customer invoice follow-ups manually via the invoice list view or via the "Send" button on the invoice form view. Automatic invoice reminders can be enabled and managed in the Accounting settings.

Download invoice attachments

Download a zip file from the invoice list view with all the attachments that were generated for the selected invoices (PDF, XML, etc.).

Employee Expenses menu item

The standalone "Employee Expenses" menu item has been removed to unify accounting flows: Approved expenses now generate draft bills in the default expense journal, allowing accountants to filter, review, and post them alongside standard vendor receipts.

Exchange entries

Exchange gains and losses are now consolidated into a single line per invoice, which can be expanded to view the calculation details.

Improved duplicate detection

Invoice duplicate detection has been improved with two levels of warnings for invoices and bills: red invoices that are highly likely to be duplicates that should be corrected and yellow potential duplicates that should be investigated before posting. After posting, only red warnings will remain (available from 19.0).

Intercompany purchase order matching

When synchronizing sales orders, purchase orders, and invoices between companies, synchronized vendor bills are now automatically matched with synchronized purchase orders.

Intuitive payment status

Payment statuses have been renamed from "In Process" to "Paid" and from "Paid" to "Reconciled" to clarify the flow. The manual "Mark as Reconciled" button has been moved to the action menu to encourage linking payments with real bank entries via the bank matching menu.

Inventory valuation

In perpetual accounting, accrual entries that impact inventory valuation can be made directly from the inventory valuation closing report: Bills To Receive, Billed Not Received, Invoices To Be Issued, and Invoiced Not Delivered. The generated journal entry directly impacts the inventory valuation account instead of transiting through the stock variation account.

Inventory valuation: COGS update

When a product's cost changes after it has been delivered/used (for example, via a landed cost, because the bill price was different from the purchase price), the delivery's value (and the invoice cost, when using perpetual accounting) is updated retroactively.

Invoice email attachments

When sending an invoice by email, documents from the Documents app (available from 19.0), or documents previously added to the chatter can be easily added as attachments.

Manual reconciliation

Manual reconciliation can now be performed on any account. The "Allow Reconciliation" checkbox has been renamed "Payment Reconciliation"; this checkbox allows open items in that account to be suggested in the bank reconciliation view and determines if "Exchange Rate Difference" journal entries are expected upon reconciliation.

Multi-ledger consolidation

Journals are now included instead of excluded in multi-ledger consolidation.

Multiple invoice details

When sending multiple invoices at once, the popup window identifies how each invoice will be sent.

New currency: Caribbean Guilder

The Caribbean Guilder (XCG) has been added to the list of supported currencies and is set as the default currency for Curaçao and Sint-Maarten (available from 18.0).

Onboarding improvements

The Accounting onboarding has been improved to ease e-invoicing setup and tax configuration.

Optimized data entry

Encoding is now faster thanks to quick reconciliation model shortcuts and statement transaction batching.

PAIN version setting

The PAIN version setting for outgoing payments has been moved from the journal level to the payment method level to offer more flexibility.

Parent accounts

Account groups have been replaced by parent accounts as the method of structuring charts of accounts. Account codes are now optional.

Pay bills from Odoo

Pay bills (individually or in batches) with a single signature directly from Odoo via the new PISP (payment initiation service providers) interface.

Peppol global location identifiers

Add GLN (global location identifiers) to partners' delivery contacts for use with Peppol (available from 17.0).

Prevent double payments in the payment wizard

To prevent double payments, the payment wizard now detects pending ("Paid") payments, automatically deducts them from the amount due, and sorts outstanding payments by date for a clearer timeline.

Professional percentage for receipts

The "Professional" percentage column that was available for vendor bills is now also available for purchase receipts (available from 19.0).

Purchase order matching

Vendor bill and purchase order matching has been improved, including the "Auto-Complete" option checking for matching bill lines before creating new ones; a clear summary of what has been matched and a warning if the price and quantity are different than expected; and the possibility to unmatch a vendor bill and purchase order.

Reminder workflow

The workflow for both automatic and manual reminders has been simplified and improved.

Reset to Draft action in list views

In any list view, reset entries to draft in batch using the new "Reset to Draft" action.

Run Auto Reconciliation

Manually retrigger the automatic bank reconciliation with the "Run Auto Reconciliation" option.

Simplification of asset models

Asset models have been replaced by depreciation models, focusing specifically on the calculation logic of fixed assets. Furthermore, asset-related configurations and tracking have been consolidated: essential information and automated behaviors are located on asset accounts themselves.

Split items on invoices

Open an invoice's journal items from the smart button and select one or more lines to split. This improves handling of multiple quantities and asset creation, as well as splitting an amount across different accounts.

Taxes in fiscal positions

Taxes are only allowed in their respective fiscal positions, meaning that if a default tax on a product or account does not belong to the fiscal position set on an invoice, the tax is removed, even if there is no replacement.

Valuation without Inventory

It is now possible to manage product stock on hand and accounting valuation without the Inventory app.

Withholding tax on payment improvements

On bills that involve a withholding tax, the due amount is broken down to show the "Amount Due," "Withhold Due," and "Net Due." Additionally, a radio button in the payment wizard allows you to pay only the withholding amount, only the payment amount, or the total amount.

## Localizations

Argentina 

Accounting:

- The VAT Book PDF report has been adapted to remove extraneous headers and footers, facilitating compliance with the legal requirement to copy the report into official, pre-numbered ledger books.

- The payment amount is automatically computed when using checks and withholdings on vendor payments (available from 18.0).

- Automatically sync with ARCA to fetch the official daily currency rate.

- The "Payment on Informed CBU" legend option has been added to comply with ARCA RG 5762/2025.

- Export the SICORE report file for Earning/Profit taxes.

- When a "Pre-Printed Journal" is selected for printing, a warning message clarifies that headers and footers are intentionally omitted from the printed PDF; such documents are typically printed on corporate stationery.

- Management of third-party checks has been improved. Inter-branch transfers are now allowed, operations are ordered chronologically rather than by check ID, and payment validations and UI labels have been refined.

- Support has been added for withholding from payments in foreign currencies and standalone withholdings with no payment.

- Export the daily book (libro diario) in Excel format from the general ledger.

Inventory:

- Use batches to mass-process Remitos Digitales.

- The vendor's Remito (Delivery Guide) number can now be entered manually on receipts.

Armenia 

Accounting: State names and codes have been added in compliance with the ISO 3166-2 standard (available from 19.0).

Australia 

Accounting: Bank synchronization now includes a new open banking integration through Basiq.

Azerbaijan 

Accounting: The state names and codes have been added in compliance with the ISO 3166-2 standard (available from 19.0).

Bahrain 

Accounting: State codes have been updated in compliance with the ISO 3166-2 standard.

Bangladesh 

Accounting:
- The outdated tax report has been removed, and tax rates have been updated to comply with the 2026 Finance Act.

- Tax groups have been restructured by their nature rather than percentage, classifying them into three primary legal pillars: Value Added Tax, Tax Deducted at Source, and VAT Deducted at Source.

- The standard chart of accounts has been updated with parent groups to ensure automatic account roll-ups and enable section-wise trial balance reporting.

Belgium 

Payroll:

- The Dimona flow is now supported (available from 19.0) and has been automated.

- The "Joint Committee" object and all associated data have been added.

- Joint committees can now be part of a parent joint committee.

- Add a maximum yearly remuneration to make an employee eligible for private car reimbursements.

- Automatically or manually add reorganization measures to working schedules based on content.

- The monthly salary structures have been merged into a single "Regular Pay" structure, with adapted rules for students and PFI.

- Remuneration for company directors is now supported.

- Temporarily dismiss warnings in the Payroll dashboard without discarding them permanently.

- New reporting has been added for meal voucher ordering.

- Configure the employee's contribution per meal voucher.

- Payslip computation for CP302 Flexi-Jobs (FLX) is now supported.

- The 7.67% holiday pay and the 28% employer contribution are automated.

- The annual earning caps (including 2026 updates) are monitored to trigger withholding taxes and ensure DMFA/Fiscal compliance.

- End-of-year bonus computation for CP302 is now supported.

- The Profit Sharing Bonus is now supported.

Brazil 

Accounting:

- Generate NF-e for exportation of goods in BRL.

- Generate NFS-e for exportation of services in BRL.

- NFS-e (Services): Specify the place of service using the customer's delivery address (available from 19.0).

- NF-e (Goods): Include the delivery address automatically when it differs from the customer's main address (available from 19.0).

- Import NFS-e PDFs and digitize them with OCR technology.

- Batch download NF-e and NFS-e XML files from the list view.

- New fiscal fields have been added to the Operation Type for both the issuer and recipient to better handle complex, non-standard ICMS ST and tax credit scenarios in NF-e and goods tax calculation.

- A text link has been added to payment QR codes to allow customers to copy/paste them to their banking app.

- A breakdown of taxes applied to individual products is available in the sales order's and invoice's chatter so it can be easily shared with customers.

- CST and calculation base amounts have been added to sales order and invoice lines in the tax details section.

- "Customer Order Number" and "Customer Order Number ID" are now included on Avalara requests and sales orders.

- Avalara's CST classification for ICMS, PIS, COFINS, and IPI can now be manually overwritten, if needed.

- Alphanumeric CNPJs are now supported, in line with the 2026 tax reform (available from 17.0).

- The "cClassTrib" field has been added to the "Taxes Settings" tab of operation types, allowing users to override the default Avalara tax classification code when submitting electronic invoices.

- The "CBS Presumed Credit" and "IBS Presumed Credit" fields are only visible when the "Tax Regime" field is set to "Simplified". The deprecated "CBS/IBS Taxpayer" setting has been removed.

- Fiscal workflows have been streamlined with reorganized fiscal fields, improved fiscal code searching, and better error handling.

Inventory:

- The "Purpose of Use," "SPED Fiscal Product Type," "Source of Origin," and "IS Taxable" fields have been added to operation types to satisfy more use cases. The setup of these fiscal fields for customer transactions has been improved.

- NF-e information is shared when using Correios and creating shipping labels with Envia.

Point of Sale: Cancel NFC-e from PoS orders.

Canada 

Accounting:

- Detailed expense accounts have been added and asset models have been reworked and localized to improve user onboarding.

- Process customer invoice payments in batches through CPA 005 Pre-Authorized Debit (PAD) files.

Chile 

Accounting:

- Detailed expense accounts and asset models have been added.

- The XML reader is now available for sales journals. Customer invoices can be imported via XML, for example, during go-live processes.

- Chilean commune data has been added, reducing manual entry errors when configuring addresses in the Contacts and eCommerce flows.

- The DTE email server and XML reader now support non-billable amounts on invoices.

- Configure branches to share CAF files, assign them to specific branches, and redirect vendor bills using internal codes.

- The F29 tax report now uses Odoo's tax calculations account tags and follows Chilean standards for easier recording in the SII portal.

Inventory: Copies of the delivery guide for yielding purposes are now supported, complying with the SII layout requirements.

China 

Accounting:

- The chart of accounts, balance sheet, and profit and loss statement have been improved (available from 19.0).

- Asset models have been added according to China Corporate Income Tax Regulations Article 60.

- Parent accounts have been updated for maintaining parent-child account hierarchy.

- The voucher template has been improved, and batch printing is now supported.

- The handling of value-added tax has been improved, and the VAT and Surcharges Return for General Taxpayers is now available.

- When applying VAT differential taxation, deductible amounts cannow be entered directly on invoice lines, and output VAT offset entries are automatically posted.

Colombia 

Accounting:

- Detailed expense accounts and asset models have been added and localized to improve the onboarding experience.

- Electronic invoicing of free samples and promotional items is supported.

- The layout of fiscal PDF documents has been improved, and the QR code is visible on all pages.

- Electronic invoicing is now exclusively handled via the free DIAN connection; Carvajal is no longer supported.

- Type 3 Contingency invoicing is now supported, enabling fiscal document generation during extended DIAN service outages.

- Withholding certification reports no longer displays the DIAN compliance message to reduce confusion.

- The user interface has been improved for a smoother experience and better error handling. Demo data has also been updated.

- Duplicate detection for vendor bills is now based on detecting CUFE duplicates.

- An example of the most common self-withholding tax and tax group has been added to the Colombian localization module. The default configuration now complies with DIAN requirements.

- The XML reader now supports withholding taxes and applies them as required on vendor bills.

- Generate exogenous information reports 1001, 1003, 1005, 1006, 1007, 1008, and 1009.

- Mandate invoices can now be created for goods as well as services. In addition, multiple principals are now allowed and are assigned correctly to journal items.

- XML generation of invoices that include global discounts and loyalty features (i.e., negative lines) is now supported.

- Vendor bills can now be imported to purchase journals using the official ZIP file.

Dominican Republic 

Accounting:

- Electronic invoicing is now supported (e-CF types 3134), with XML generation and submission to DGII via Infile.

- Export the 606 purchase report as a TXT file.

- Tax grids and reporting rules have been updated for the IT-1 (ITBIS declaration) report.

- Use the national identification number (cédula de identidad) alongside the RNC (registro nacional de contribuyentes) number, which enables automatic electronic invoicing directly from the eCommerce flow.

Ecuador 

Accounting:

- Add a custom legend to the header of PDF reports for electronic documents for cases such as exporters and NGOs.

- The chart of accounts has been improved by removing duplicate account names and types for a cleaner and more accurate financial structure.

- The display of the address and customer information on the invoice PDF has been improved.

- New withholding taxes are available that are based on the Resolución NRO. NAC-DGERCGC26-00000009 (available from 17.0).

- The electronic invoice provider's RUC is added to electronic documents and printed representations (RIDE) to comply with the SRI Resolution NAC-DGERCGC26-00000027 (available from 17.0).

Point of Sale: The Point of Sale app now blocks sales above the legal limit when using the default "Consumidor Final" customer, requiring selection of a real customer for compliant invoicing.

Egypt 

Accounting:

- ETA synchronization is integrated into the "Send" wizard, with pre-check validation banners, and trackable submission history. A "Demo Mode" has been added to internally test the workflow without any credentials.

- Batch fetch ETA PDFs from the list view.

- The chart of accounts has been updated to implement a scalable 6-digit structure and group parent accounts. Default account mappings have been improved for deferred revenue/expenses, and expense accounts.

- Select a branch's activity type by its code and Arabic description to align with activity types on the Egyptian Tax Authority's (ETA) portal.

- Obsolete schedule taxes and other taxes have been removed and the related reports have been deleted.

- The VAT return and withholding reports have been updated to ensure ETA compliance.

- Withholding taxes can now be managed using the new "Deducted Withholding" and "Gross Withholding" (gross-up) flows to handle both standard deductions and net-of-tax agreements.

- The ETA Code field visibility has been restricted to only the "Sales" type taxes.

- Additional units of measure (square meter, milliliter, tonne, minute, millimeter, kilowatt-hour, square foot) are now mapped to their Egyptian Tax Authority (ETA) unit codes.

- Set a product's item code per company.

- Helwan and 6th of October states have been removed and existing records have been migrated to Cairo and Giza.

- "Building Number" is now visible on the Company and Contact forms.

- The "Street" and "Street 2" fields of partner addresses are concatenated into a single payload string to ensure address details are included during ETA synchronization.

Payroll:

- A calendar-day working schedule has been added to support payroll and leave calculations based on calendar days.

- Calculations for overtime have been improved.

- Payslips include end-of-year income tax adjustments.

- Export formats for Form 1, Form 2, and Form 6 are available for the National Organization for Social Insurance (NOSI) and Form 2 for the Egyptian Tax Authority (ETA).

- The management of attendance-based employees and salary rules has been improved to better align with labor laws and handle edge cases.

Point of Sale: Sales and refund receipts are submitted to the Egyptian Tax Authority (ETA) as e-receipts.

France 

Accounting:

- Pre-configured annual report templates (plaquettes) compliant with official PCG standards have been added, featuring auto-populated financial sections, customizable layouts, and help textto simplify mandatory financial disclosures.

- The endpoints in the annuaire that match the SIREN entered in the FRCTC field are now detected automatically and are available for selection if multiple ones exist.

Georgia 

Accounting:

- The base localization package has been added, including a localized chart of accounts, taxes, and VATreport (available from 19.0).

- State names and codes for Georgia have been added in compliance with the ISO 3166-2 standard (available from 19.0).

Guatemala 

Accounting:

- Support has been added for Factura Especial (FESP), enabling automatic withholding of 100% VAT and ISR, generation of mandatory legal phrases, and FEL certification for purchases where the supplier does not invoice (available from 18.0).

- FEL documents can now be canceled directly via Infile. The cancellation reason is recorded and the document is marked as "Anulado" in SAT.

- The IDP fuel tax calculation for regular gasoline has been updated to account for the 10% alcohol content of E10 fuel.

- Official VAT Sales and Purchase reports (Libros de IVA) have been added in SAT-compliant formats.

eCommerce: Support has been added for electronic invoicing, including issuer phrases and allowing invoices to be issued to final consumers (CF).

Point of Sale: The Point of Sale app has been updated to comply with local electronic invoicing requirements, including mandatory validations and data needed for proper FEL/DTE issuance (available from 19.0).

Hong Kong 

Accounting: The chart of accounts and the profit and loss and balance sheet reports have been updated to ensure full compliance with the HKFRS for Private Entities (PEs).

Payroll:

- Define an Internet fee and apply it to all employee payslips.

- Set an average daily wage (ADW) for employees.

- Calculate pre-transition and post-transition offset amounts for SP/LSP payments.

- Generate monthly MPF reports, create payroll groups/member classes, and define contribution periods for each employee.

- Define the month for end-of-year payments. Calculations are based on the number of previously generated payslips.

- Generate IR56B/E/F/G tax forms in XML and PDF format in compliance with the Inland Revenue Department (IRD) technical specifications.

- HSBC Autopay has been integrated directly into the standard reporting workflow. Select it as an export format and configure any Autopay account for any report.

- A new "Rentals" system has been introduced to manage the end-to-end rental process. Employees can submit rental requests and monthly proofs of payment directly via the Employees app, replacing manual forms and external spreadsheets. The system supports various Hong-Kong specific scenarios, including direct employer payments and employee-led contributions, while centralizing all compliance documentation for HR review.

- Define a minimum duration of consecutive leave (e.g., 4 days for paid sick leave) to automate eligibility for the 80% ADW leave types and prevent invalid requests.

- Daily, weekly, bi-weekly, and semi-monthly pay schedules have been added. Salary rules for MPF and fixed allowances automatically scale to the selected period, ensuring compliance with Hong Kong statutory thresholds for non-monthly earners.

- A new salary structure is available for casual employees in the catering and construction industries, supporting specific MPF Industry Scheme rates.

- The IR56M report is now available, as well as a new salary structure for non-employees such as contractors, freelancers, artists, etc.

- IR56 reports now support adding a different postal address to the employee's private address.

- A warning for the new "468 Rule" is shown when employees on non-continuous contracts approach or meet continuous contract thresholds.

- Customized salary rules are now automatically mapped with IRD reports (IR56B, IR56E, IR56F, IR56G, and IR56M) through the use of pre-defined categories.

- IR56 employer tax forms are now approved for submission to the Hong Kong Inland Revenue Department (IRD).

- XML exports of the IR56 form automatically group original and non-original submissions (A/R/S), in accordance with IRD requirements.

- Sick leave related to work injuries is now supported, in accordance with the Employees' Compensation Ordinance (Cap. 282).

- Support for the BOC banking report for FPS Non-Payment Type has been added.

- Different termination structures (Payment in Lieu of Notice, Severance Payment, and Long Service Payment) have been merged into salary rules within employee pay structures.

Hungary 

Accounting:

- Generate an A60 statement, the Hungarian-specific EC sales list.

- 
Synchronize received vendor bills directly from the NAV API. Odoo automatically fetches and updates received invoices based on the info in NAV (available from 18.0).

India 

Accounting:

- Issue credit notes for price adjustments.

- A new bill of entry flow for imported goods simplifies the recording of import customs duty and ensures the duty amount is included in inventory costing through landed costs.

- Apply TDS more flexibly with support for TDS & Payment, TDS Only, or Payment Only, giving you more control over tax deduction and payment processing.

- GST compliance has been extended to Composition Taxpayers, with dedicated support for the Composition Scheme and its statutory returns, CMP-08 and GSTR-4.

- Prepare financial statements with the prescribed presentation, classification, and disclosures as required under Schedule III, Division I of the Companies Act.
- GST-compliant self-invoices can now be generated and printed for transactions where the recipient is required to issue a self-invoice under the Reverse Charge Mechanism (RCM). The self-invoice includes the required tax details and provides a dedicated document format for statutory compliance.
- The number of invoices pending for e-invoicing is now displayed on the Accounting Dashboard. Clicking the link displays an overview of the pending items, from where they can be sent individually or in bulk.
- Support for the Quarterly Return Monthly Payment scheme (QRMP) scheme had been added, with quarterly GSTR-1 filing and IFF for the first and second months of the quarter.
Payroll:
- Employees can now generate and print their TDS declarations from their employee profile.
- A new Gratuity Calculation Report provides a clear and accurate view of employees gratuity eligibility and, if relevant, the payable amount based on the applicable statutory regulations.
- Flexible sandwich leave options allow "Weekends and Company holidays," Weekends Only," or "Company holidays Only" to be included when calculating leaves taken across non-working days.
- A new EPF Summary report provides a comprehensive overview of employees' EPF contributions and balances for each employee.
- In the "End of collaboration" wizard, a new "Notice Period" tab has been added. In addition, when the dismissal date and actual departure date are the same, the Full and Final Settlement (FNF) payslip can be generated directly.
- TDS challans can now be generated monthly to record and deposit TDS deducted from employees salaries. Form 138 can be generated quarterly to report the TDS deducted and deposited with the Income Tax Department. 
- A salary configurator is now available, allowing applicants to select benefits while signing the offer.
- Generate an HDFC eNET file from individual payslips or a pay run and upload the file to the HDFC bank portal to streamline the payroll process and speed up salary payment.

Indonesia 

Accounting:

- The chart of accounts and asset list have been updated to include detailed expense accounts and localized asset models.

- Multiple improvements have been made to the contact form, including removing the "Is PKP" field and merging the "NIK" and "NPWP" fields.

- A new tax and tax group have been added for compliance with the PPN Dipungut mechanism.

Payroll:

- Default accounts are now set for standard payroll rules.

- Overtime calculation (PP No. 35/2021) - overtime components are now excluded from the basic salary calculation and are instead processed under a separate rule.

Point of Sale: QRIS is now available as the kiosk QR payment option.

Iraq 

Payroll: The base payroll localization has been added, including monthly pay salary structure, social insurance, leaves setup, end-of-service benefit calculation, and overtime rates calculations.

Italy 

Accounting: Add notes (causale) and attachments (allegati) directly to electronic invoices.

Japan 

Accounting:

- The balance sheet and profit and loss statement are now compliant with Chusho Kaikei Yoryou.

- The chart of accounts has been updated to cover major business cases and to comply with Chusho Kaikei Yoryou and the e-Tax coding system.

- A consumption tax section has been added to the tax return for general and simple taxpayers, covering variants such as accumulation, deduction, and itemized methods.

Jordan 

Accounting:

- Error handling for sending invoices to JoFotara has been improved, now including validations for the supplier's country and TIN.

- The JoFotara credit note synchronization has been improved by refining the line-matching logic (available from 17.0).

- Support has been added for "Transit," "Foreign Trade," and "Free Zone Transfer" invoice types (available from 17.0).

- The UX of the JoFotara EDI has been improved: Related fields are grouped under the "Other Info" tab on the invoice, and errors are now directly handled in the "Send" wizard.

- Multiple JoFotara submission scenarios are supported for invoices and POS receipts.

Point of Sale:

- The JoFotara refund synchronization has been improved by refining the line-matching logic (available from 17.0).

- Generate JoFotara-compliant receipts (available from 18.0). 

Kazakhstan 

Accounting: State names and codes have been added in compliance with the ISO 3166-2 standard (available from 19.0).

Korea 

Accounting:

- The Korean VAT reports for General and Simplified Taxpayers have been improved.

- The new "Issuance Type" field on invoices routes tax data directly to specific report lines, replacing the need for multiple redundant tax configurations.

Kuwait 

Accounting: State names and codes have been added in compliance with the ISO 3166-2 standard.

Payroll: The base localization package has been added, including regular pay, social insurance calculations, end-of-service and provision calculations, and leaves setup.

Kyrgyzstan 

Accounting: State names and codes have been added in compliance with the ISO 3166-2 standard (available from 19.0).

Lebanon 

Accounting: State codes have been updated in compliance with the ISO 3166-2 standard and duplicate state entries have been removed.

Lithuania 

Payroll: Existing fields and other inputs have been converted to the new salary input flow.

Luxembourg 

Payroll: The Decsal and Decmal reports are supported.

Malaysia 

Accounting:

- Consolidated invoices can now be issued within the invoicing and accounting workflow.

- MyInvois is now supported across branches, with improved handling for sole proprietorships.

Point of Sale: Self-service e-invoicing via MyInvois is now possible.

Mexico 

Accounting:

- On Global Invoices, bimonthly periodicity is now available, and the month is now selectable, making invoicing past months' orders easier.

- A cancellation acknowledgment can now be generated for canceled CFDIs.

- Invoices with the external trade complement can now include services as well (available from 19.0).

- Minimum wage, tax rates, and UMA have been updated.

- Factoring payments have been added in the bank reconciliation view.

- A new localized balance sheet report has been added based on the NIF B-6.

- A new localized profit and loss report has been added based on the NIF B-3.

- Complementary trial balance XML reports can now be generated.

- Upload a FIEL to automatically download missing vendor bills via the SAT integration.

- The XML reader now identifies the goods purchased, suggests the account, offers smarter product matching, and validates the amounts in Odoo against the XML to detect discrepancies.

Inventory: The driver for a bill of lading can now be selected directly in the delivery order, allowing for easier driver switching.

Payroll: When a new payslip is generated, the email sent to the employee now also includes a link to the Comprobante Fiscal Digital por Internet (CFDI) in XML format (available from 19.0).

Oman 

Accounting: State codes have been updated in compliance with the ISO 3166-2 standard.

Payroll:

- The base localization package has been added, including regular pay, social insurance, end-of-service and provision calculations, leaves setup, overtime rules, and employer net cost calculations.

- Support for generating Wage Protection System (WPS) files has been added to facilitate salary payments and reporting in compliance with Omani legal requirements.

Pakistan 

Accounting:

- Expense account descriptions have been added.

- State names and codes have been updated in compliance with the ISO 3166-2 standard.

- The chart of accounts has been updated with parent groups to ensure automatic account roll-ups, enable section-by-section trial balance reporting, and simplify compliance with the Companies Act 2017.

- The chart of accounts has been updated and outdated VAT and withholding reports have been removed to prepare for upcoming FBR Form-7 compliant reporting.

- The balance sheet and profit and loss statement are now generated dynamically using chart of accounts prefixes, rather than using legacy prefix mapping.

- Tax configurations have been streamlined by consolidating obsolete taxes into statutory sales tax, further tax, and withholding tax pillars to align with the Sales Tax Act.

- The Pakistani Rupee (PKR) currency symbol is displayed before the amount to align with standard market practices (available from 19.0).

- Accounting demo data has been updated.
- Maximum retail price tax calculations are supported.

- The 18% Third Schedule tax formula has been updated to handle tax-inclusive calculations.

- The "NTN" field on the contact form has been renamed to "Business Identification Number," which accepts both NTN and CNIC to classify a contact as a business.

- A new "Consumer Identification" field has been added to classify a contact as an individual customer, ensuring a clear distinction between the two.

- An integration with the Federal Board of Revenue (FBR) e-invoicing system has been added for registered businesses to ensure compliance with S.R.O. 1852(I)/2025.

Payroll: Payslips include end-of-year income tax adjustments.

Point of Sale: An integration with the Federal Board of Revenue's (FBR) computerized system is now available to ensure real-time reporting of point-of-sale orders, which is mandatory for all Tier-1 retailers in Pakistan.

Peru 

Accounting:

- Support has been added for 19 sub-books of the Peruvian Inventory and Balances Electronic Book, including Trial Balance, Cash Flow, and specialized account reporting for SUNAT compliance (available from 18.0).

- A GRE can now be generated natively for internal transfers, with the operation type field visible and set to "11 - Transfer Between Warehouses" by default.

- Electronic vendor withholding documents can now be created with their corresponding XML, CDR, and PDF files.

- Support has been added for the Fixed Amount ISC (System Type 02) for electronic invoices.

Point of Sale: SUNAT-compliant thermal printing has been added for Peruvian POS electronic invoices and receipts, generating legal electronic document representations (Factura/Boleta/Notas de Crédito) directly on 58/80 mm printers (available from 19.0).

Philippines 

Accounting:

- The format of the partner ledger report now complies with the Bureau of Internal Revenue (BIR) requirements. The Book of Accounts has been added for CBA and CAS compliance in the Philippines.

- Generate and download official PDF certificates for specific partners and date ranges using newly added report variants for BIR 2306 (Final Withholding Tax) and BIR 2307 (Expanded Withholding Tax).

- Differentiate between individuals and companies with the "Entity type" field. An information banner appears on Philippine reports that include a contact without a set entity type.

- Generate BIR 2306 and BIR 2307 withholding tax certificates on vendor bills.

- A new disbursement voucher has been added for internal company use.

- The BIR1600VT report has been implemented for final withholding VAT transactions.

- Send BIR 2306 and BIR 2307 withholding certificates directly to the vendor, if requested, when a vendor bill is paid.

- Invoices are now BIR- and CAS-compliant.

- Statutory discounts for senior citizens and people with disabilities are now supported on invoices and credit notes.

- The 2551Q report is now available for non-VAT registered companies.

- Names are now auto-separated into "First Name," "Middle Name," and "Last Name" to ensure the correct format for BIR reports.

Payroll:

- The basic Philippines payroll package is available with mandatory calculations for basic pay, benefits, taxes, overtime, etc.

- Support has been added for Form 1601-C items for BIR monthly payroll reporting.

- The yearly BIR Form 2316 tax report has been added.

- Support has been added for auto loan deduction rules for SSS Salary loans, SSS Calamity loans, Pag-IBIG Multi-Purpose loans, Pag-IBIG Calamity loans, and Pag-IBIG Housing loans.

- Annual tax annualization reporting has been added with auto-generated BIR Form 1604-C summary worksheets and validated Alphalist DAT file exports compliant with BIR eSubmission standards.

Qatar 

Accounting: State names and codes have been added in compliance with the ISO 3166-2 standard.

Romania 

Accounting:

- Support has been added for CPV codes on products in eFactura (available from 17.0).

- The VAT rates and the VAT report have been updated (available from 17.0).

- When downloading an invoice from ANAF, if the XML doesn't include a PDF, Odoo will download the official ANAF-generated PDF and use it as the attachment (available from 18.0).

- Select a period when manually synchronizing invoices with ANAF.

- Generate the SAF-T with Stock variant from the general ledger report (available from 18.0).

- Generate the D300 VAT report in XML format for submission to the tax authorities (available from 19.0).

- If an invoice is rejected by the SPV, it can now be reset to draft, corrected, and sent again (available from 18.0).

Saudi Arabia 

Accounting:

- Expense accounts have been reworked, and asset models have been added to improve the onboarding experience.

- The invoice date now serves as the official ZATCA issuing date, replacing the confirmation date (available from 17.0).

- The Additional Identification Number field is now available for non-Saudi contacts, in compliance with the BR-KSA-81 ZATCA rule (available from 18.0).

- Identification Scheme and Identification Number are now also used to distinguish between a company (TIN, CRN, MOM, MLS, 700, SAG, OTH) and an individual contact (NAT, GCC, IQA, PAS).

- The "Is Retention" checkbox has been removed, and a negative sales tax is now automatically classified as a retention tax.

- States have been adapted to reflect the 13 subdivisions.

- Demo data has been improved.

- ZATCA synchronization has been integrated into the "Send" wizard, with pre-check validations shown in a banner; synchronization history is recorded in a new ZATCA tab upon sending. Batch processing through the list view is now supported.

- Invoice reports have been updated to include a line-level "Discount Amount" column, and the "Amount Due" label has been changed to "Invoice Total Payable Amount."

- Add multiple partner identifiers, including Saudi-specific identifiers like the Saudi national ID number, Iqama number, or GCC ID number, using the multi-ID feature on a partner's contact form.

- Define invoice types ("Tax" or "Simplified") and transaction types ("Export," "Summary," or "Nominal") directly on the invoice form to explicitly control API routing (B2B versus B2C).

- A new "Supply End Date" field has been added under the "Other Info" tab to generate simplified nominal invoices and to record continuous supplies and multiple deliveries.

- Configure tax exemption reasons using standard UBL tax category fields to simplify and declutter tax setup. Support has been added for free-text exemption reasons when recording services outside the scope of tax (VATEX-SA-OOS).

- ZATCA address compliance has been improved by restricting the "Building Number" and newly renamed "Secondary Number" (formerly "Plot Identification") fields to exactly four numerical digits, preventing XML validation errors (BR-KSA-37) during submission.

- The duplicate "Supply Date" field has been removed from the invoice form header to avoid confusion, keeping it accessible only within the "Other Info" tab.

- The standard chart of accounts has been updated to include a scalable 6-digit structure and to group parent accounts based on reverse liquidity sequencing.

- The default account mappings have been improved for deferred revenue, deferred expenses, and expense accounts.

- Invoices being sent to ZATCA after a timeout or a ZATCA server outage are no longer blocked.

- The "Rounding Method" setting is now fixed to document-level rounding to improve ZATCA compliance.

- Support for e-invoicing in ecommerce transactions has been added (available from 19.0).

Payroll:

- Track employees disciplinary actions linked to payslip deductions.

- Identify late attendance and apply salary deductions accordingly.

- Integrate with the GOSI platform to retrieve contribution portions.

- The end-of-service computation has been improved to better align with the QIWA and Ministry of Labor regulations.

- Issue advance salary payments when an employee takes annual leave.

- Automatically generate the GOSI Wages Update Sheet to easily update the contribution base on the GOSI portal whenever employee salaries change.

- Compute the total end-of-service benefit liability for one or more employees at any point in time using the End-of-Service Benefit report.

- A calendar-day working schedule has been added to support payroll and leave calculations based on calendar days.

- Automate sick leave deduction calculations based on the employee's year-to-date consumed balance to ensure alignment with labor laws.

- The calculation for end-of-service provisions has been updated to better support termination cases under Article 77 of the labor law.

- A "Month (30 days)" scheduled pay option has been added to calculate wages and unpaid leave deductions strictly on a 30-day basis, managing varying calendar months in compliance with labor laws.

Point of Sale:

- Support for down payments has been enhanced.

- The ZATCA PDF is no longer generated during order validation, avoiding unnecessary waiting time. It can be generated on demand when the invoice is first viewed or downloaded (available from 18.0).

Singapore 

Accounting:

- GST taxes have been refined to align with current governmental requirements and to prepare for future GST InvoiceNow document compliance (available from 19.0).

- Tax invoice, credit note, and customer accounting PDF reports have been added to comply with the Inland Revenue Authority of Singapore (IRAS) GST requirements.

- The chart of accounts, balance sheet, and profit and loss statement have been improved to comply with Singapore Financial Reporting Standards (SFRS).

Sri Lanka 

Accounting:

- A new localization package is available for Sri Lanka, including the chart of accounts, taxes, balance sheet, profit and loss reports, VAT 001 report, and WHT 001 report.

- The format of the VAT report has been improved to display data more clearly.

- The sequence format and layout of tax invoices now comply with the requirements outlined in Gazette No. 2481/22 (available from 19.0).

Taiwan 

Accounting:

- The chart of accounts, balance sheet, and profit and loss statement have been updated (available from 19.0).

- Taxes and tax reports 401, 403, and 404 have been updated.

- ECPay e-invoicing details can now be specified directly on quotations and sales orders for B2C transactions, allowing information to be collected earlier in the sales flow.

eCommerce: ECPay integration is now supported to issue and submit Taiwanese e-invoices for eCommerce transactions (available from 18.0).

Point of Sale: Issue e-invoices for Point of Sale, and submit them to the official government portal via integration with ECPay (available from 19.0).

Tajikistan 

Accounting: State names and codes have been added in compliance with the ISO 3166-2 standard (available from 19.0).

Thailand 

Accounting:

- The chart of accounts has been updated to comply with TFRS for NPAEs, introducing detailed expense accounts, asset models, and VAT accounts. Relevant default accounts, taxes, and tax groups have been updated accordingly (available from 19.0).

- The P.P.30 VAT return report can now be exported as a CSV file compatible with the Thai Revenue Department's RD Prep software for monthly filing.

- The Company ID label has been updated and repositioned to clarify its use as a branch code, alongside new input validation.

- Taxes, tax groups, and fiscal positions have been expanded, adding new withholding types and enabling cash basis by default for services. Related descriptions have also been translated.

- The official 50 Tawi withholding tax certificate can now be generated and downloaded in PDF format.

- The PND 3 and PND 53 CSV export files have been reworked to ensure full compliance with the RD Prep application requirements.

- Tax grids have been updated to have more intuitive and user-friendly names.

- The Withholding Tax Summary Report has been added to provide a detailed breakdown of all taxes withheld, simplifying ledger reconciliation before final government submission.

- Tax invoices can now be generated independently from commercial invoices, with their own numbering sequence. Combined receipt/tax invoices are also fully supported.

- Debit and credit note PDF reports have been updated to fully comply with Thai regulations.

Türkiye 

Accounting:

- Additional Invoice Scenarios (Basic, Export and Public), Invoice Types (Sales, Withholding, Tax Exempt, and Registered for Export), and Tax Offices have been added (available from 18.0).

- Return and withholding return invoice types are supported through credit notes.

- Support has been added for commercial invoice scenarios and sending export invoices as draft including PDF previews, approval, acceptance, rejection, and cancellation.

- Reason code 702 on Registered for Export invoices is now supported, including Customer and Seller Line Codes per invoice line.

- The street fields have been combined into a single address line when generating the XMLs (available from 19.0).

- The Nilvera e-invoice experience has been improved with clearer field guidance and validation, automatic contact matching and PDF retrieval, streamlined setup, and invoice status synchronization.

- E-invoices with an Error status can be canceled (available from 19.0).

- CTSP validation has been restricted to invoices marked as "GİB Product Export Invoice" and the field label has been updated for clearer guidance.

- Commonly used Stamp Tax rates (0.948%, 0.189%, 0.759%) have been added and included in the Tax Report.

- State codes have been updated to comply with the ISO 3166-2 standard.

- Exemption reason 351 has been added by default for sales invoices with 0% VAT.

- 35 new accounts have been added and account types 27 and 28 have been updated to "Fixed Assets".

- Parent accounts have been introduced and linked to corresponding sub-accounts in accordance with GIBs 7/A chart of accounts.

- Partner identifiers, such as MERSIS numbers, are no longer managed using tags, but instead via the multi-ID feature on a partner's contact form.

- For public sector e-invoices, the tax IDs of both the public institution and the public spending unit can be added to an invoice.

- Tax office information has been added to the "Türkiye - Accounting" module for ease of use across business processes.

- Support has been added for multiple invoice sequences per journal based on invoice characteristics.

- The General Ledger CSV export has been fixed to maintain a continuous sequence of journal entries throughout the fiscal period (available from 17.0).

- Reconciliation letters have been added with a localized customer statement layout.

- Credit notes created from customer invoices use the journal's "Default Return from Sales Account" (available from 18.0).

- The accuracy of automatic exchange rate updates has been improved by using selling rates for currency conversions (available from 17.0).

- Synchronization with Nilvera has been improved to ensure all eligible invoices and bills are retrieved regardless of date or record limits (available from 19.0).

- Subscription e-invoice handling has been improved for mixed invoices by deriving document dates from subscription lines.

- Support has been added for fetching buying and selling rates and the option to choose rates per transaction.

- Rounding issues affecting foreign currency amounts in words on e-invoices have been fixed (available from 17.0).

- The Nilvera e-Invoicing integration is now only available with Odoo Enterprise.

- Support has beed added for issuing e-invoices to customers through government-approved dummy Tax IDs and to e-invoice customers for export invoices.

- Support has beed added for cancelling e-Archive invoices directly from Odoo.

- Support has beed added for website sales channel on e-Archive invoices.

- Automatically derive the 3-character e-Dispatch prefix from the delivery order sequence.

- The withholding tax configuration has been simplified by moving the withholding reason selection to the invoice level and reducing the number of predefined taxes.

Inventory:

- The Nilvera e-Dispatch integration has been expanded to send, receive, and fetch e-Dispatch documents and PDFs directly from Odoo.

- The Nilvera e-Dispatch integration has been expanded to send e-Dispatch responses directly from receipt records, allowing users to accept or reject dispatched goods.

- Upload incoming e-despatch XML files from the Nilvera portal to create draft inventory receipts (available from 18.0).

- Tax office information has been added to the e-Dispatch XML.

- The Nilvera e-Dispatch integration has been enhanced by enabling the mapping of one or multiple related dispatch documents to an invoice, ensuring dispatch orders are reflected in the XML.

- An "Invoice Serves as e-Dispatch" option has been added, allowing e-Archive invoices to serve as the dispatch document while tracking inventory operations.

Payroll:

- An advanced salary structure was introduced, supporting sick leaves, salary advances, and annual leave advance payments.

- Calculate severance pay in line with Türkiye's labor laws.

- The Wage-Related Withholding and Premium Service Declaration (1003B) has been added to report employee payroll tax and social security contribution information.

- Support has been added for R&D and design center incentives to automatically calculate stamp tax exemptions and education-based income tax reductions.

- Support has been added for self-insured (4b) employees, ensuring accurate salary calculations in compliance with labor laws.

Turkmenistan 

Accounting: State names and codes have been added in compliance with the ISO 3166-2 standard (available from 19.0).

United Arab Emirates 

Accounting:

- Expense accounts have been reworked, and asset models have been added to improve the onboarding experience.
- Generate the Federal Tax Authority (FTA) VAT audit file from the general ledger (available from 19.0).

- Expense account descriptions have been added.

- The chart of accounts has been redesigned to comply with IFRS and UAE Commercial Companies Law. It features a scalable 6-digit numbering system and reverse-liquidity sequencing, and includes statutory equity reserves, essential technical accounts (WIP, Goods in Transit), and parent account groups.

Payroll:

- The employee benefit contributions have been revised to better align with GPSSA & ADPF's regulations. Sick leaves are managed through a single salary rule and a single leave type.

- Treatment of DIFC Employee Workplace Savings (DEWS) contributions has been adapted to align with Dubai International Financial Centre (DIFC) regulations.

- A new "Emiratization Compliance" report has been introduced to track Emiratization percentages in line with MoHRE regulations.

- Nonsalary employer cost items (e.g., insurance, work permits, visa processing fees) are now supported, improving labour cost visibility.

- The WPS export has been enhanced to better align with MoHRE regulations (available from 19.0).

- Calculations for annual leave and end-of-service provisions have been improved (available from 19.0).

- Calculations for overtime have been improved.

- The plane tickets benefit calculation has been added to the salary rules.

- A calendar-day working schedule has been added to support payroll and leave calculations based on calendar days.

- Compute the total end-of-service benefit liability for one or more employees at any point in time using the End-of-Service Benefit report.

United Kingdom 

Accounting:
- HMRC authentication is now saved per company rather than per user, enabling multiple team members to manage different client accounts simultaneously.
- 
It is now possible to submit VAT returns for multiple companies without constant re-authentication.

United States of America 

Accounting:

- Each pre-configured asset model now has dedicated depreciation and expense accounts, replacing the previous use of shared generic accounts.

- A new integration method for AvaTax is available: "Avalara Included." This integration offers a more affordable option for small and medium-sized businesses, while maintaining full tax computation capabilities for the United States and Canada.
- Avalara tax parameters are now automatically added while calculating taxes to support state-specific tax calculation requirements.

- The US Sales Tax Report has been redesigned to include multi-jurisdiction breakdowns, state summary rollups, and enhanced support for exemptions and non-taxable goods.

Payroll:

- Local taxes for New York City and Yonkers were added to the US Payroll localization.

- Support has been added for qualified overtime deduction rules under the "One Big Beautiful Bill," including a new salary rule and parameters to display capped overtime deduction information on employee payslips (available from 19.0).

- Support for Georgia, Iowa, Kansas, Kentucky, Michigan, Mississippi, Missouri, New Jersey, South Carolina, Tennessee, and Utah has been added, including state, county, and city tax rules and payroll configurations.

Time Off: The default US leave types have been expanded and improved to better align with standard workplace policies.

Uruguay 

Accounting:

- A DGI lookup action has been added for Uruguayan partners, allowing users to fetch and update official taxpayer data from Uruware directly from the identification number.

- Products and services can now be configured as non-billable, allowing them to be included onelectronic invoices.
Point of Sale: Electronic invoicing is now supported, including electronic sales documents, refunds, and customer identification requirements.

Uzbekistan 

Accounting:

- The base localization package has been added, including a localized chart of accounts, taxes (VAT 12%, Export 0%, Exempt 0%), and standard financial reports: balance sheet and profit and loss (available from 19.0).

- The Uzbekistani Som (UZS) currency symbol has been updated to "so'm," which is used by the Central Bank of Uzbekistan.

- The Russian language is now supported in reports and in the chart of accounts (available from 19.0).

- The VAT Registry Number is displayed on Uzbek invoices using the 14-digit PINFL instead of the TIN for B2C invoices (available from 19.0).

Vietnam 

Accounting: 

- The chart of accounts and balance sheet have been updated following Circular 99/2025/TT-BTC on corporate accounting guidelines (available from 18.0). 

- The Tax Declaration Form 01/GTGT report has been added to ensure compliance with local tax regulations.

- Electronic Internal Transfer Notes can now be issued via SInvoice.

- Parent accounts can now be used to structure the chart of accounts, allowing child accounts to be grouped for reporting and visualization.

- Appendix 142 has been added to Tax Declaration Form 01/GTGT to ensure compliance with local tax regulations.

- Export the 01/GTGT report and Appendix 142 in XML format for easy import into HTKK software or direct submission via the tax portal.

- Tax tags have been updated to offer more intuitive and user-friendly naming.

- The General Journal - S03a-DN and General Ledger - S03b-DN reports with counterpart account information have been added to comply with Circular 99/2025/TT-BTC.

Point of Sale: E-invoices can now be issued via SInvoice for POS orders, ensuring compliance with local tax regulations (available from 18.0).

## AI

IAP credits required

All AI features now need credits, which can be bought via IAP.

Agentic automation

- Agents can now update themselves to best serve their use cases.
- Agents can be summoned by automated and scheduled actions.
- Check automation chats to see how agents handled them, and ask for changes based on the errors it faced.
- 
Agents get avatars based on their assigned role.

AI agents: ask questions about a file

Ask questions about a file while previewing it.

AI agents: create records

Ask an AI agent to create anything for you, including by uploading a file of instructions like a PDF.

AI agents: feedback

Agents now show specific, live updates on what they are doing, instead of a generic "AI is thinking" message.

AI agents: filter by time period

AI agents can filter by time period (e.g., weeks, quarters, etc.) when building views.

AI agents: image generation

Ask an AI agent to generate images and enriched buttons for websites and mailings.

AI agents: reprocess sources

Manually ask AI agents to reprocess sources and use their most up-to-date version.

AI agents: send files

Upload files and link documents in AI agent chats.

AI agents: update records

Ask an AI agent to update records.

AI chat request options

Use the + menu to add extra options to a request before sending it in an AI chat.

Automatic AI model selection

Select an AI provider and Odoo automatically uses the best AI model for the task at hand.

Connect Odoo to anything

Connect to your database via MCP (model context protocol).

Default prompts

Add context and dynamic values in default prompts.

Interactive agent responses

Respond to AI Agents' questions, permission requests, and suggestions by clicking buttons.

Model-bound agents

When an agent is asked to interact with a specific model, it suggests being the default agent of that model.

Preview cards in live chat

When an AI agent mentions supported records in a live chat conversation, the chat can show rich, clickable cards instead of simple record names.

Stored conversations

Conversations with agents are now stored for up to 30 days after the last message was sent.

Tool call limit confirmation

If an agent reaches its maximum tool call limit, it prompts the user for confirmation before proceeding.

Topics renamed to skills

"Topics," i.e., collections of instructions and tools that define how an AI agent behaves within a specific context, are now called "skills."

Voice interaction

Dictate requests directly to an AI agent.

## Appointments

Accessory products for appointments

Appointments now supports the selection of accessory products during checkout.

Appointment booking flow

Booking a user or resource as the first step now happens on the same page as the selection of the date and time.

Appointment names

View the customer name, appointment type, and number of seats, where relevant, directly on booked appointments in the Gantt and calendar views.

Appointment page design

Appointment pages have been refreshed with a cleaner, easier-to-use design on both mobile and desktop.

Appointment rescheduling

Reschedule booked appointments in just a few clicks.

Appointments calendar view

Create bookings and assign resources or users for any appointment type from any schedule calendar view.

Attendance at a glance

See how many people are expected for each time slot directly in the Gantt view.

Automatic resource assignment

Automatic resource assignment for a booking now accounts for the number of participants.

Availability display

Availabilities are now displayed accurately in the Gantt and calendar views of appointments across different appointment types, accounting for private events marked as busy.

Booking capacity control

Set a booking limit per time slot to control how many appointments can start at the same time.

Booking shortcuts

Access bookings directly from the Kanban view, with shortcuts opening the most relevant view for bookings awaiting confirmation, upcoming appointments, or todays schedule.

Closed days management

Closed days are now easier to manage across the Appointments app.

Consistent booking page design

Booking pages now match the Website apps default styling, even when the Website app is not installed.

Customer feedback

Request feedback from customers after their appointments.

Default party size

For appointments with multiple attendees, the booking flow now preselects two guests by default.

Maximum capacity override

Manually override an appointment type's maximum capacity when needed.

Performance improvements

Various performance improvements, including optimized resource and date availability calculations, have made every part of the booking process faster.

Web page redesign

Appointment web pages now have a more consistent look and feel with respect to other Odoo web pages. Relevant information is displayed throughout the booking flow for improved clarity.

Website editor blocks

Website editor blocks can now be added anywhere on Appointment pages.

## Appraisals

Bulk actions

Perform actions on appraisals in bulk, such as confirming, sending by email, marking as done, or resetting multiple appraisals at once.

External 360 feedbacks

Send 360 feedback requests to any contact, not just employees.

Next appraisal date

Managers can edit the next appraisal date on the employee profile (without specific rights).

Parent goal progression

Follow the progression of a parent goal based on the progression of its sub-goals.

Smart template selection

New appraisals now default to the appraisal template matching the employee's department, if applicable, or to the first template not linked to a department.

## Attendances

Automatic check-out time

Employees with no working hours set (flexible) can now define a specific automatic check-out time.

Color-coded indicators in Gantt view

Indicators in the Gantt view now provide clearer insights into worked hours by highlighting underworked, expected, and overtime hours with intuitive values and color coding.

eSSL and Mantra integration

eSSL and Mantra biometric devices can now be integrated directly with Odoo Attendances to receive employee attendance records directly in Odoo.

Overtime analysis report

Analyze overtime and find which rules applied to each overtime with the new overtime analysis report.

Overtime rulesets

Easily link new employees to an overtime ruleset using a new smart button.

Photo at check-in

If the check-in device has a camera, take a photo of employees when they check in.

Prevent app use

Users without Attendances access rights can no longer use the app.

Public holiday option ruleset

Define and compute overtime rates for public holidays in Attendances rulesets.

Single check-in mode

A "Single Check-in Attendance System" can now be enabled alongside automatic check-out, recording only an employee's first check-in during scheduled working hours.

Time-based filters

Filter and group attendance records by working schedule and day.

## Barcode

Backorders

Choose whether or not to create a backorder from the Barcode app.

Barcode product creation

Scan a product's barcode to create products via Barcode Lookup throughout the app.

Batch receipts

Batches are suggested when receiving products from the same vendor but with multiple expected receipts.

Bulk lot/serial number generation

Generate lots or serial numbers in bulk.

Fixed button positions

Barcode buttons now stay in the same position on the screen, regardless of the entered quantity, to improve usability and reduce errors.

Inventory count

When doing an inventory count from Barcode, click the pen to manually enter the total counted quantity for a selected product.

Light users

Restrict light users' access to just the Barcode module.

Manual entry

Manually enter a lot number and the related quantity when scanning it is not possible.

Manufacturing operations

Improvements have been made for manufacturing, including removing irrelevant settings and correcting behavior to ensure that reserved lot/serial numbers of components are shown only if the related setting is active, and that mandatory scans do not prevent editing of by-products.

Mobile: user login button

The user login button has been added to the mobile view.

Packages: pre-encoded contents

Packages and their contents communicated by the vendor can now be pre-encoded in the Inventory app. When receiving the package, users can scan the package to mark the package and its contents as received.

Packages: untracked goods

If a package contains untracked goods registered in a previous step, they are included in the contents of the package when it gets scanned in Barcode in later steps.

## Blog

Blog module redesign

More styles and options have been added for blog posts, blog landing pages, and blog category pages.

Recommended post

Display a "Recommended Post" at the end of a blog post.

Scheduled blog posts

Followers are notified by email when a scheduled blog post is published.

## Calendar

Booking Pages

In the Booking Pages section of the sidebar:

- select an appointment type to directly create new events for that appointment type,
- copy an invite link for an appointment type to your clipboard, or
- modify the available time slots or options for an appointment type

Google and Outlook synchronization

Calendar synchronization with Google Calendar and Microsoft Outlook is now significantly faster and more reliable.

Google Calendar sync

Work locations set in Google Calendar are now reflected in Odoo Calendar.

Linked records

Link a Calendar event to any record in Odoo.

Manage pending activities

Visualize and manage pending activities via the Calendar app.

Meeting title time detection

When a new event is created and the title includes the time (e.g., 2 PM Meeting), the event is now automatically booked at the correct time.

Mobile calendar redesign

Navigate the Calendar app more easily on mobile devices thanks to improved touch interactions and better use of screen space.

Multiple calendars in one place

Create and manage multiple calendars in Odoo, including shared team calendars.

Sync multiple Google calendars

You can now sync multiple Google calendars with Odoo, with changes reflected both ways.

View other people's calendars

Easily view other people's calendars using the "Meet with" feature in the sidebar, then create new events that automatically add them as participants.

## Contacts

Contact enrichment

Enrich contact information for one or multiple contacts.

Hierarchical view

Visualize contacts in a new hierarchical view.

## CRM

Hidden address info

Address details have been hidden to encourage the creation of a contact.

Lead distribution

Set sales team members' lead auto-assignment rules to "Always in rotation," "In rotation with a limit," or "Out of rotation."

Lead generation

- The Generate Leads feature now suggests more ways to generate leads.

- Lead generation now uses the Dun & Bradstreet database of companies instead of Clearbit, providing better worldwide coverage and increased data quality.

Pipeline switcher

Switch between teams' sales pipelines using the new dropdown.

Upsells from lead

Access a customer's existing subscriptions directly from their leads. Create upsells and renewal quotations directly from existing subscriptions and link them automatically to the customer's opportunity/lead.

## Dashboards

Billing targets vs billable time

Analyze billing targets versus actual billable time in the Timesheets dashboard.

Carousel data layer

Include a range of cells in a carousel using a data layer, making dashboard content responsive and visible on mobile devices.

Favorite filters

Save global filters as favorites and set a default global filter to quickly display the data you need.

Fiscal year date filter

The Fiscal Year date filter has been added.

Frozen share links

Manage frozen share links from the dedicated menu in the Dashboards app.

Private dashboards

Create private dashboards and provide access to specific users.

Region selector for geo charts

When the default region of a geo chart is set to "World," a region selector allows Dashboards users to see data for a specific region.

## Discuss

"On a call" status

See if another user is currently on a call using the Phone app via their Discuss status.

Call transcripts

Record Discuss calls and get summaries of what was said.

Channel categories

Organize channels into categories.

Favorite channels

Add channels to your favorites to find them faster.

Mark notifications as unread

Mark notifications as unread when handling them in Odoo.

Polls

Create polls in your conversations.

## Documents

Access right management

Define groups of users to streamline management of access rights for files and folders.

Auto-sort fleet documents

Automatically sort fleet documents and link them to the corresponding vehicle based on the license plate.

Bank statement attachments

Attachments on bank statements are now available in both the Accounting and Documents apps.

Branch management

Branches are now handled in the bridge between Accounting and Documents.

Document request upload notification

When a requested document or documents are uploaded, the requester is now notified.

Employee documents

The Employees folder structure has been improved: payslips are now sent to a dedicated folder, with a shortcut added to each employee's My Drive. The integration between Employees and Documents is now enabled by default.

Markdown previews and thumbnails

Previews and thumbnails of markdown files are now supported.

Multi-file document request

Request multiple files in a single document request.

Rename files with AI

Use AI to rename files uploaded in Documents.

## eCommerce

AI-assisted product editing

Ask the AI assistant to generate product images or to change a product name, price, or description from the Shop page.

Attribute filters

eCommerce filters now exclude values not relevant to the loaded page.

Automated cross-sell suggestions

Automatically generate accessories, optional, and alternative products for your eCommerce products.

Automated review requests

Automate customer review request emails and define how long after the order confirmation the request should be sent.

Default journal for eCommerce orders

Select a default accounting journal for all eCommerce orders.

Donations

Donations are now integrated into the eCommerce cart flow, enabling recurring donations via subscriptions.

Dynamic product building block

The layout of the dynamic product building block has been improved.

Email gift card directly to recipient

Customers can now email a gift card directly to a recipient after finalizing a purchase.

External identifiers

Set external identifiers on attributes to match specific platform requirements for data feeds and microdata.

Extra step granularity

Show the extra step in the checkout process only when the cart contains products from selected categories.

Gelato: variant images

It is now possible to add specific print images per variant.

Google Analytics events

GA4 eCommerce event tracking coverage and data quality have been improved to fully comply with the Google Analytics 4 specifications.

Guest orders: contact creation

Contacts created based on guest orders on the e-commerce are now archived by default and unarchived if the end user creates an account.

Location selector: country filter

Customers can filter pickup points by country in the selection dialog.

Loyalty progress bar

Progress bars have been added in cart notifications and the cart summary, showing progress towards unlocking a reward or success when the reward is unlocked.

Minimum product quantity

Set a minimum order quantity for e-commerce products.

Mondial Relay handled via Sendcloud

The Mondial Relay pickup point module for eCommerce has been removed. Mondial Relay is still available alongside other pickup points via Sendcloud.

Open attribute value on search

When searching for a product using an attribute value, e.g., black t-shirt, clicking the product opens it with the specific attribute value selected.

Order dashboard

A new dashboard in the order list view provides a quick overview of e-commerce orders and their status.

Pay Now button

Edit and translate all "Pay now" buttons in the checkout.

Preferred delivery date

Allow customers to select a preferred delivery date.

Prevent sales by category

Prevent sales for an entire category while still displaying the price of its products.

Pricelist selector

The pricelist selector is now available on all pages of an e-commerce website.

Product reference price

The product reference price is now displayed on all e-commerce pages by default.

Product features

Ribbons, comparison price, attributes, and secondary images are now visible on dynamic product building blocks.

Product variant thumbnails

Enable the "Show Thumbnails" field on attributes to use product variant images instead of the attribute values displays.

Products grid building block

A new grid version of the Products building block is now available.

Range filter for attribute values

Use the Range display type for numerical attributes to allow customers to filter products using a range slider.

Restrict packagings per website

Units and packagings can now be restricted per website.

Return management

Allow customers to return products from the customer portal.

Ribbon filters

Two new ribbon filters, "On Sale" and "In Stock," are now available.

Simplified inventory management

eCommerce can now work with simplified inventory management, without installing the Inventory app. A single on-hand quantity is displayed per product and there are no required transfers.

Standalone categories

Prevent specific e-commerce categories from being displayed on the Shop page in order to generate specific website sections with curated product selections.

Stock-based product publishing

Automatically unpublish and republish products based on their stock availability.

Taxes included/excluded price display

Set whether the displayed prices include or exclude taxes at pricelist level to serve B2C and B2B customers on the same website.

Variant image management

Variant images can now be managed centrally from the "eCommerce" tab of the product form.

WhatsApp abandoned cart follow-up

Send a WhatsApp message to follow up on abandoned carts.

Withdrawal requests

A new action is available for contact forms to manage withdrawal requests.

## eLearning

Course access from portal

Customers can view and access their e-learning courses from the customer portal.

## Email Marketing

Click tracking

See which recipients clicked on which links in a mailing.

Conditional content

Blocks in a mailing can be shown based on conditions for individual recipients.

Contact management

Add contacts to marketing mailing lists more easily, and allow them to manage their own subscriptions.

Dynamic mailing lists

Create dynamic mailing lists whose recipients are calculated when the mailing is processed.

Employee/supplier mailings

Send mailings to employees and suppliers.

Favorite blocks

Save mailing blocks to reuse them later.

Full-screen editing

Editing mailing designs is now always done in full screen.

Link tracking

Disable link tracking directly in the html editor.

Mailing template library

Save mailings as templates and access them via a dedicated menu.

New fonts

New fonts have been added, with a fallback system for cases where the selected font is not supported by a mail client or browser.

New templates and blocks

New templates and blocks have been added.

Product and event snippets

Drag and drop product and event snippets and select the relevant records to automatically fill them.

Social media accounts

Connect your social media accounts and customize their appearance in mailings.

UTM reference

UTM handling now aligns with industry standards. Instead of creating new sources on the fly, details are mapped via a new "Reference" field.

## Employees

Employee directory

In a multi-company database, all users can now see the employees of all companies in the database.

Salary simulation

Simulate an employee's salary through their profile, with all necessary information pre-filled.

Remote Work

The Remote Work module has been merged into the Employee app.

Single-signer HR documents

HR users can now sign single-signer documents with employee details automatically filled in based on the document configuration, then send the signed document directly to the employee.

Variable working schedule

Working schedules can be defined from the calendar mode to precisely encode periods of variable work time.

## ESG

Assign emission factors with AI

Use AI to automatically assign the most appropriate emission factor to emissions.

## Events

Event combos

Combine event registration and food/beverages in a combo ticket with distinct VAT rates.

## Expenses

Consolidated expenses report

Select and print multiple expenses at once in a single consolidated report.

Expense limits per job position

Define maximum allowable expense amounts per job position. Managers can cap expenses at the authorized limit before granting final approval.

Salary rules for expense products

Expense products can now be assigned the appropriate salary rule to ensure the correct calculation for the type of expense.

## Field Service

Field Service merged into Planning

The Field Service app has been discontinued. Its features are now integrated into the Planning app.

## Fleet

Driver assignment

When selecting or changing a car for an employee in the Payroll app, the vehicle's assigned driver is updated automatically.

## Frontdesk

Member entry management

Limit check-ins exclusively to visitors who are members, with the option to restrict access to one or more specific membership tiers.

## Helpdesk

Filter unanswered tickets

Identify unanswered tickets by filtering tickets where the most recent message is from the customer.

Reminder email before auto-closing tickets

Send customers a reminder email before their ticket is automatically closed.

Similar ticket detection

Detect similar helpdesk tickets using AI to provide teams with suggested answers.

## Inventory 

Allocation report

The allocation flow has been improved with the ability to allocate directly from the forecast report and a new design of the allocation report.

CMR document

Download a CMR (Convention on the Contract for the International Carriage of Goods by Road) from deliveries that is prefilled based on information from the transfer.

Company-specific customer lead times

Customer lead time for a product can now be different for each company.

Intercompany flows

Intercompany flows have been improved:

- Use routes to resupply one company from another.

- Deliver a sales order from another company's warehouse (or use another company's pickup points).

- Valuation costs of transferred products are properly transferred between companies.

- Updates of linked sales and purchase orders (price, quantity, etc.) are properly communicated to the other company.

Inventory at a past date

Use the new date picker in the stock report to consult an improved overview of inventory at a past date that preserves any filters and allows a precise timestamp.

Landed costs for specific products

Add landed costs to individual products of a transfer.

Location-specific push routes

Create push routes based on the specific destination of the product. For example, decide your product should follow route A if you receive it in WH/Input/A, and route B if you receive it in WH/Input/B.

Picking notifications

Subscribe to notifications about a transfer's status.

Preview Barcode instructions in operation type

Preview the Barcode instructions that are indicated to the operator based on the chosen configurations directly from the operation type.

Product packaging barcodes

Access and manage product packaging barcodes more easily from the product form.

Product replenishment

The "Order" and "Order to max" buttons have been consolidated into a single "Order" button in the replenishment dashboard. When reordering products in advance (i.e., selecting products without the "To reorder" filter), the order quantity is calculated as the max quantity minus the forecast quantity.

Inventory valuation adjustments

Product cost adjustments and their impact on inventory valuation are now clearly listed in a new "Valuation Adjustments" menu, available in debug mode. In perpetual accounting, journal entries are automatically created.

Sendcloud: pickup points

Pickup points proposed by Sendcloud carriers can now be selected directly from a sales order or a transfer. These can also be corrected after an eCommerce order has been placed.

Simplified returns

The return wizard has been removed, and the returns process has been simplified.

Sendcloud: package reference

The package reference from Odoo is now sent to Sendcloud so it can be displayed on the labels of carriers that support package references.

ShipStation shipping connector

An integration with shipping and fulfillment platform ShipStation has been added, allowing shipping costs to be calculated and shipping labels and tracking numbers to be retrieved directly through Odoo.

Stock aging report

Access a stock aging report from the "Moves Analysis" pivot view.

Suggested stock levels for reordering rules

Minimum and maximum stock levels for reordering rules are now suggested based on demand history, desired minimum days of coverage, and order frequency.

Variant-specific HS codes

HS (harmonized system) codes are now set at the level of product variants.

Traceability Report

The Traceability Report has been improved to display upstream and downstream lot/serial numbers in a single report. Final lot locations are highlighted, while intermediary locations are dimmed.

Variant-specific packagings

Different packagings can be added for specific variants.

Vendor purchase reference

The vendor purchase reference now appears on receipt transfers to improve traceability and help warehouse operators identify the vendor from incoming transfers without having to refer to the related purchase order.

ZPL location barcodes

Location barcode labels are now available in ZPL format.

## Knowledge

Article settings quick access

Right-click on an article in the sidebar tree to change its settings.

## Live Chat

Connect social media messaging

Connect Messenger and Instagram DMs directly to live chat teams so they can reply from Odoo.

## Maintenance

Maintenance requests: Gantt views

Equipment and work center requests are now available in dedicated Gantt views.

Maintenance teams on stages

Customize maintenance stages for each maintenance team.

UX improvements

Several UX improvements have been made, including a new "Status" widget.

## Manufacturing

Backorder planning

Backorders of planned productions are now also planned automatically upon their creation.

Bill of materials

- Compare bills of materials based on the product quantities used.

- The "Status" and "Availability" columns in the bill of materials overview forecast have been merged to improve clarity.

- Register extra costs for a manufacturing order using the dedicated field on the bill of materials form.

- View and manage a bill of materials' components and sub-assemblies directly from the bill of materials form.

Component replacement

The "Used In" smart button on the product form now displays respective bill of materials component lines, simplifying component replacements and other changes. The action to display component lines is also available from the bill of materials list and form views.

Continuous production

Produced quantities can now be recorded on work orders. If "Continuous Production" is enabled on the bill of materials, subsequent operations start as soon as some quantities are ready.

Draft versus confirmed manufacturing orders

On the "Manufacturing" operation type, choose whether procurement creates draft or confirmed manufacturing orders.

Flexible consumption

The flexible consumption setting on bills of materials has been removed. All manufacturing orders use flexible consumption by default, regardless of whether or not a bill of materials is selected.

Generate lots and serials when closing manufacturing orders

Lots and serial numbers are always automatically generated when closing manufacturing orders.

Lot/serial number transfers

The "Transfers" smart button on lots and serial numbers shows delivery move lines instead of the entire transfer, allowing for more precise recalls.

Manufacturing order Kanban view

The manufacturing order Kanban view has been redesigned. Cards are now grouped by scheduled date in weeks and display information such as component status, active work center, deadline, and remaining time. The total remaining time for all manufacturing orders is displayed in all "Group By" searches.

Manufacturing orders planned ASAP

Manufacturing orders are now planned as soon as possible by default. When multiple orders are planned, their sequence in the list determines scheduling priority.

MO cost

- The "MO Cost" field on an MO overview now shows the provisional cost when the manufacturing order is in progress and real cost when the manufacturing order is complete.

- The cost of subcontracted product replenishments in the MO overview takes into account component costs.

Produce button

The separate "Produce" and "Produce All" buttons have been replaced by a single "Produce" button in Manufacturing, Shop Floor, and Barcode.

Put in pack from MO

If a destination package is specified on the manufacturing order, the final product is automatically added to it upon production.

Reset MO to draft

Reset validated or canceled manufacturing orders to draft.

Shop Floor demo sheet

Download a Shop Floor demo sheet to quickly test the app's barcode capabilities.

Split manufacturing orders

Split ongoing manufacturing orders to produce the remaining amount later.

Subcontracting MO

The "Component Status" and MO overview are now also available for subcontracting production. Check what components are missing and order them directly.

Subcontracting reception valuation

If no purchase order is linked, the subcontractor cost is assigned at the product's reception based on the vendor pricelist.

To Replenish filter

A "To Replenish" filter has been added to the MO Overview.

Traceability report expiration dates

Expiration dates have been added to the traceability report.

Visualize and confirm work orders

Confirm work orders from the Work Order Planning Gantt view, even if they contain draft work orders.

Work and manufacturing order reporting

The reporting of work orders and manufacturing orders has been improved, with key metrics updated.

Work order views

- Visualize and adapt work order planning with the new work order Kanban view.

- The new Gantt view allows the planning of work orders using drag-and-drop, with enhanced color coding and the ability to view planning by employee.

- Completed work orders are now displayed by default in the Gantt view.

Work center overview

The work center overview has also been updated, featuring quick links to work orders and maintenance planning.

Work orders in blocked work centers

It is now possible to process work orders in blocked work centers.

## Marketing Automation

AI-assisted campaign creation

Ask the AI assistant to help set up campaigns.

Event-based triggers

Marketing automation campaigns can now be triggered by events such as page visits, list subscriptions, and more.

Mailing template library

Save mailings as templates and access them via a dedicated menu.

New triggers and actions

Trigger actions by event, date, anniversary, webhook, or on demand. New actions include "Collect new activity" and "Wait for a value."

Webhook integration

Connect external systems to marketing campaigns using webhooks to automatically add participants in real time.

Workflow builder

A drag-and-drop workflow builder has been added, offering a more visual way to create journeys.

## Marketing Card

Default target URLs

For event-related campaigns, a default event URL is now suggested.

Event app integration

Send cards directly from an event via the new "Send cards" button; in the wizard, recipients can be defined using conditions.

Language support

Select the language in which the card should be sent.

## Online Payments

ACH payments

Tokenization is now available with ACH payments.

Authorize.net

Authorize.net now supports webhooks for delayed payment confirmations.

ECPay

An integration has been added with ECPay, an online payment provider for Taiwan (available from 19.0).

Mollie

- The following payment methods are now supported for Mollie: Apple Pay, Blik, in3, Google Pay, Klarna, MB WAY, Multibanco, and Swish.

- Payment methods can be tokenized for recurring payments.

Pay on Invoice provider

A new "Pay on Invoice" payment provider is available, allowing customers to confirm their orders without requiring immediate payment.

Payment provider views

The payment provider views have been refined and simplified for a smoother user experience.

PayPal

The PayPal payment provider now manages card payments, alternative payment methods, tokenization, and OAuth for merchant authentication.

PayU

PayU is now available as a payment provider for India.

Pricelist and amount restriction

Restrict the use of a payment provider based on pricelists and minimum/maximum amounts.

Redsys

Payment methods can now be tokenized for recurring payments.

SOFORT

SOFORT is no longer supported as a payment method by any payment provider.

Stripe

The following payment methods are now supported for Stripe: Alma, Apple Pay, Canadian Pre-Authorized (ACSS) Debit, Google Pay, Satispay, and Swish.

Toss Payments

A new online payment provider, Toss Payments, is available for South Korea.

Wero

Wero is now supported as a payment method for Buckaroo and Worldline.

Wire transfers

Wire transfer payments are now automatically confirmed based on bank transactions.

Xendit

Xendit now supports payments in Malaysia, Thailand, and Vietnam (available from 18.0).

## Payroll

Chatter on pay runs

The chatter is now enabled on pay runs to allow more interaction.

Contract date modification

It is now impossible to modify the contract dates of an employee if it impacts existing payslips. Instead, revert those payslips in order to modify the contract dates.

Dashboard

The Payroll dashboard has been revamped to focus on warnings for setup and day-to-day control of pay.

Driver assignment

When selecting or changing a car for an employee, the vehicle's assigned driver is updated automatically in the Fleet app.

Employee type per company or branch

Restrict an employee type to a specific company or branch.

Net to gross simulation

Perform a salary simulation based on a net amount.

Pay run workflow

The "Pay Runs" workflow has been enhanced to provide a clear, step-by-step process for creating and validating pay runs.

Payslip sending options

Choose when payslips are sent to employees (on confirmation, on payment, or manually).

Payslip UX

The "Salary Computation" tab of payslips has been improved to show only relevant information.

Salary indexation

Increase an employee's salary by a percentage and/or a fixed amount.

Salary rules

Salary rules can now belong to multiple categories and be used in multiple structures.

Test print pay runs

Test a pay run in one click with printed payslips.

Time entry exports

All time entry exports now follow a standard format.

Work entries removal

Work entries and the Planning-Payroll integration have been removed. Work entry types and time off types have been merged.

Working schedules

Use the updated working schedules to define a time per day without specifying start and end times.

## Phone

Audio settings during a call

The audio sources for the microphone and speaker can now also be selected during an ongoing call.

Auto-fill and auto-log

Opening the softphone widget automatically fills the keypad with the current record's phone number, and logs the call on that record if the numbers match.

Call flow designer

Design a flow for incoming calls with extensions, call menus, time- and location-specific steps, and other PBX features.

Call logging in chatter

Calls can be logged in the chatter during or after each call.

Call transfer to another device

Move ongoing calls from one device to another one without interruption.

Choose outgoing number

Assign multiple numbers to a user and let them choose which one to use as the outgoing number (caller ID).

Floating widget

Open the softphone as a floating, draggable widget that overlays content.

Linphone

Provision Linphone with a QR code.

Push notifications

Push notifications are sent for incoming calls.

Simultaneous calling

Start or receive a new call during an ongoing call.

## Planning

Employee-specific materials

Assign materials to employees; when an employee is assigned to a shift, their assigned material is automatically added to the shift's resources.

Field Service: auto plan

Customer locations are taken into account for automatic shift planning.

Field Service: customer equipment 

- Track whether customer equipment is covered by a maintenance contract via Subscriptions.

- Access documentation for the equipment installed at the customer's location directly from shifts.

Field Service: customer ratings

Track customer satisfaction for onsite interventions.

Field Service: intervention confirmation email

Choose whether to automatically send customers a confirmation email upon publication of a shift to inform them that an intervention has been scheduled.

Field Service: live map

Track technicians' locations on a live map.

Field Service: map view

View both shift locations and technician availability in the map view.

Field Service: product barcodes 

Add products to a shift by scanning their barcodes.

Field Service: report 

Studio fields are shown on the field service report.

Field Service: routing preferences

Routing has been improved for the map view, allowing users to choose between an ordered mode and an optimized mode. The ordered mode calculates the routing by respecting the order of the addresses, while the optimized one calculates the shortest path between the addresses.

Field Service: track customer history

Track customer equipment and intervention history.

Field Service: travel fees

Invoice customers for distance-based travel fees or a fixed travel-time fee.

Field Service: website form

Collect service requests through a website form rather than letting customers book services directly.

Field Service: worksheets

- Worksheet templates now use property fields instead of Studio fields.

- Add worksheets to shift templates.

Gantt view: travel times

Connect to the Mapbox integrator to display travel times in the Gantt view to help schedule shifts accordingly.

Link shifts to tasks

Assign a specific task to a shift.

Multiple resource assignment

Assign multiple resources to the same shift.

Priority shifts

Set priorities on shifts. When using the "Auto Plan" feature, higher-priority shifts are scheduled first.

Schedule shifts from side panel

Schedule shifts by dragging and dropping them from the side panel into the calendar or Gantt view.

Send shifts via WhatsApp

Send employees their schedules via WhatsApp.

## PLM

BoM comparison

Compare bills of materials to visualize changes more easily.

Optional ECO version update

Version updates are optional when applying an ECO to a product or bill of materials.

Product updates

Introduce new products more easily when applying an ECO, and track product versions on the product form.

Universal ECO report

All bill of material changes are now displayed in one comprehensive report that also includes cost comparisons.

## Point of Sale

Base unit price on product label

Display the product reference price on product labels.

Booking Kanban and pivot views

Bookings now have dedicated Kanban and pivot views.

Convert order lines into a combo

The POS now recommends converting order lines into combos when possible to simplify menu orders for big tables.

Employee access levels

Employee access levels have been renamed, and a new, highly restrictive "Supervised" access level has been added.

End of session

At the end of the session, a global sale is generated to simplify the accounting entries and remove the miscellaneous entries. Payment journal entries are automatically generated at the time specified in the settings without needing to end the session.

Expired product notification

Users are notified when they select expired products on the POS.

GoFood delivery integration

Support has been added for GoFood orders and menu sync for Indonesia and Vietnam (available from 19.0).

GrabFood delivery integration

Support has been added for GrabFood orders and menu sync for Cambodia, Indonesia, Malaysia, Myanmar, Philippines, Singapore, Thailand, and Vietnam (available from 19.0).

Label printing

Any label printer can now be connected directly via USB proxy.

Mercado Pago terminal

Mercado Pago terminal-based QR payments, refunds, and cancellations are now supported via the Orders API integration, enabling use by Chilean companies as well.

Multiple currencies

Multiple currencies can now be supported at checkout in the same point of sale.

Order search

In the "Orders" overview, search by invoice number or receipt number.

Print preparation tickets per product

Use the "Split per product" feature to print one preparation ticket per product instead of one ticket grouping all products in the order.

Printer management

Set up multiple printers linked to your point of sale, and then select on which printer to print, improving the workflow for big structures with several cash drawers.

Receipt printing

A printer's paper size can now be configured manually in developer mode to ensure receipts are formatted correctly.

Reorganizing products in POS interface

Drag and drop products in the POS interface to reorganize them.

Self-ordering

- Customers using self-ordering can leave notes at checkout; these are visible on the kitchen display.

- Optional products are displayed when ordering from a mobile device or a kiosk.

- Generate and print an order-specific QR code, allowing customers to scan it to place their order and pay after their meal.

Service fees

Add a service fee for Point of Sale orders and self-orders via presets, and display the amount on invoices.

Simplified inventory management

Point of Sale can now work with simplified inventory management, without installing the Inventory app. A single on-hand quantity is displayed per product and there are no required transfers.

Simplified receipts

Print a simplified receipt that displays the subtotals and taxes of the order without the order lines.

Snooze products

Make products temporarily unavailable on the POS or self-ordering menu by snoozing them.

Static QR code payment

Payment via static UPI QR codes is now supported.

UrbanPiper integration

- Most settings related to UrbanPiper can now be managed directly from within the Point of Sale app.
- The following food delivery platforms are now supported via UrbanPiper: Didi Food, Enqueue, FoodZapp, InstaShop, Mandoob, RadYes , Smiles, Snoonu, Swiggy Bolt, Talabna, Zomato Express, Zyda.

WhatsApp and SMS self-order receipt

Customers can now receive their receipt via WhatsApp or SMS when self-ordering.

## Project

Assign project roles

Assign team members to each project role.

Assign tasks to portal users

Assign tasks to portal users who are followers of a project.

Collaborators separated from followers

Give customers access to projects in their portal without adding them as followers.

Mobile: improved task header design

The design of task headers on mobile devices has been improved.

Printable task schedule

Print a task schedule from the Gantt view.

Profitability report

The profitability report in the project dashboard has been removed. You can now analyze your projects budget, as well as actual and projected margins, using the new dedicated reports.

Projects from opportunities

Create projects from opportunities.

Task tracking in user portal

Display changes recorded in a task's chatter in the user portal.

## Purchase

Alternatives comparison

The alternatives comparison screen has been improved to make it simpler for users to choose the best option.

Default Incoterm per vendor

Select a default Incoterm for purchases on a vendor form to have that Incoterm automatically set on all quotations and purchase orders related to the vendor.

End-customer address in portal

The customer's address is shown on purchase orders on the vendor portal.

Expected arrival date

Edit the expected arrival date for goods directly from the purchase order while keeping track of the original date provided by the vendor to ensure accurate on-time delivery rate calculations.

Product unit cost versus purchase unit cost

When purchasing in a unit or packaging that differs from the product unit, the cost in the product unit is displayed beside the cost in the purchase unit in the product catalog.

Purchase agreement structure

Sections and notes can now be added in purchase agreements/blanket orders, similar to purchase orders.

Recompute Expected Arrival when deadline is in the past

When you have not been able to confirm your purchase order before the order deadline, recalculate the new expected arrival dates for products with a single click.

Total amounts on purchase order sections

Purchase orders sections and subsections can now show total amounts.

Vendor quality rate

View a vendor's quality performance based on quality checks performed on their products.

## Quality

Bypass quality checks

Quality admin users can validate inventory transfers without performing quality checks.

Failure location

Set the "Failure Location" from the form view of quality checks when a check fails.

## Recruitment

Improved integration

Integration between applicants, talent pool, and job positions has been improved, with a more user-friendly interface, better synchronization between talent and applications, and more accurate talent-to-job matching.

Job form visibility conditions

On a position's online job application form, limit a field's visibility based on whether or not the job contains specific job titles, allowing the selection of multiple jobs.

Matching score filter

Filter applicants based on their "Matching Score" to quickly identify and prioritize the most relevant applications.

View talents from job position

From a job position, view existing talents that are a great match for the position. 

## Referrals

Contact outreach tracking

Track the number of people contacted to promote a job.

## Rental

Automatic price update

Changing the pricelist or rental period on a sales order automatically impacts the prices on existing sales order lines.

Rental order creation

Rental products can now be added to a sales order; doing so automatically adds a "Rental period" field. To convert a rental order to a sales order, remove the rental start and return dates.

Rental orders dashboard

A new dashboard at the top of the list and Kanban views provides a summary of rental activity, allowing easy filtering by status.

Simplified and unified pricelists

Sales, Subscription, and Rental prices have all been unified in a single "Prices" tab using the standard pricelist engine.

Strikethrough pricing

When a pricelist-based discount is applied to a rental product, the Shop and product pages now display the original price crossed out alongside the new discounted price.

Working schedules

"Unavailability days" have been replaced by working schedules.

## Repairs

Invoice creation

Create invoices directly from repair orders if no quotation is needed.

Repair order status

The "Under Repair" status has been removed.

Repair-related services

Add services to a repair order.

## Sales

Payment-based commissions

Calculate sales commissions based on the amount paid.

Charge overages

Charge overages for prepaid services directly within the invoicing wizard.

Dashboard

A Sales dashboard has been added at the top of the quotations and sales orders list views, providing a quick view of sales performance and quick-filter buttons.

Description-only sales order lines

Sales order lines no longer require a linked product by default, and can instead be created using a description. The new "Mandatory Product" setting allows product selection to be enforced.

Editable product variant price

Edit the sales price of product variants directly.

Editable margins

Edit the margin directly on a sales order line to automatically recompute the sales price.

Fixed prepayment amounts

Set a prepayment value to a fixed amount.

Fixed Price pricelists

Extra prices from attribute values are now applied on top of "Fixed Price" pricelist rules.

Lazada

A marketplace integration with Lazada has been implemented to fetch orders and delivery slips, and to synchronize inventory (available from 19.0).

Loyalty point expiration

Set an expiration date for loyalty points.

Manager commissions

Roll up achievements and targets of individual contributors into other salespeople's commissions (e.g., managers' commissions and split commissions).

Mark orders as fully invoiced

Mark an order as fully invoiced to prevent any further actions on it.

Periodic pricing

Define pricelist rules (including surcharges) for specific periods or days of the week.

Price rules per packaging type

Define specific price rules per packaging type.

Pricelist report improvements

Pricelist reports are grouped by product category and additional fields, such as "Product Reference" and "Barcode" are included. It is also possible to filter pricelist reports by a specific date.

Product images

Display product images on sales orders and related PDFs.

Quotation sections

- Update the quantity and unit of a section to quickly update all related products.
- Add multi-line section descriptions.
- Save sections as templates so they can be reused in future quotations.

Quotation templates

- Select a quotation template directly when creating a sales order.
- Subsections and options, such as "Hide Price" and "Hide Composition", are now available.
- Browse and add products using the product catalog.
- Save any quotation or sales order as a quotation template using the "Save as template" action.

Sales order creation from purchase orders

When creating sales orders from purchase orders, the import now uses data from previously corrected orders.

Sales order email template

When sending a sales order via email, the customer reference is now included in the subject and body of the default email template.

Sales order line numbering

Number sales order lines on the sales order form, the PDF file, and the customer portal.

Sales order portal page

The sale order portal page has been redesigned for better content organization and clearer positioning.

Services & Material

Use fixed-price and cost-based services and materials for re-invoicing.

Ship orders without Inventory

Send shipping confirmation emails and manage order fulfillment of sales orders without the Inventory app.

Single-use discount codes

Allow discount codes to be used only once per customer.

Stacked fields on sales order lines

"Margin," "Margin (%)," "Delivered (%)," "Tax Excl.," and "Tax Incl." are now stacked vertically in sales order lines rather than displayed in columns.

TikTok

A marketplace integration with Tiktok has been implemented to fetch orders and delivery slips, and to synchronize inventory (available from 19.0).

## Shop Floor

HTML note field

The note field on manufacturing orders is now an HTML field in the Manufacturing backend and in Shop Floor to allow to share instructions more flexibly.

Instruction updates

When updating instructions, the current instruction is now pre-filled, making small adjustments easier.

Work center barcode

Scan a work center's barcode to select it.

## Sign

Automated signature requests

Sign requests can now be sent via automated actions. Assign fixed signers and dynamic signers based on their role (customer, salesperson, etc.).

Create new fields on the go

Create new Sign fields while preparing a document for signing and define their availability to other users.

Easier tag management

Document tagging has been improved across the entire signing flow, making documents easier to organize and find. Tags can now be viewed and edited on templates, in the list and Kanban views of sent signature requests, and on the "Details" of a signature request.

Identity verification via itsme 

It is now possible to request signer identification via itsme in over 30 European countries.

Improved progress tracking

Signature requests with more than three signers now show the full signer list and each signers status in a dropdown, making it easier to follow the signing progress.

Mobile and touchscreen support

The Sign app is now fully responsive across screen sizes, with touchscreen support for creating and editing signature documents.

Non-latin alphabets

Additional alphabets are supported, including Cyrillic, Devanagari, and Arabic, covering over 40 new countries.

Qualified Electronic Signature via itsme 

Signers can now be requested to sign using itsme's Qualified Electronic Signature (QES) in over 30 European countries. Such signatures have the legal equivalence of a handwritten signature under eIDAS regulations.

Record updates

Odoo Sign fields can now be used to update the corresponding field in an Odoo record with the information entered during the signing process.

Reorder signers

Reorder signers directly in the Sign document editor.

Send signature requests from activities

Signature Requests can now be initiated directly from activities and activity plans and are fully integrated into the Sign flow. The request appears in the chatter with relevant actions, and a log note is posted once the document is signed.

Sign mode options menu

Signers can use the options menu to print, delegate, or refuse a document while viewing it.

Sign templates

- Sign templates can now include predefined CC recipients, and template ownership can be reassigned directly from the template configuration.

- Select any sent signature document and save it as a template or send it to another contact.

- Export answers from a Sign template in bulk directly from the template. The export generates an Odoo spreadsheet with all signer data.

- Sign templates now allow updating the underlying document without recreating the template. Existing signature fields are automatically preserved in their original positions.

Signed PDFs: bookmarks and settings

Bookmarks and relevant PDF settings, such as metadata, are preserved from the original uploaded document in the final signed PDF (available from 18.0).

Touchscreen support

Use your finger or a stylus to drag new Sign items, reposition existing ones, and interact with the Sign document editor on a touchscreen.

## Social Marketing

Add mentions in social media posts

Insert mentions in social media posts to notify accounts or tag people.

AI assistant

Use AI to write social media posts.

Facebook and Instagram stories

Post stories on Facebook and Instagram from Odoo.

Image display order

Choose the display order for images when posting multiple images.

Personal LinkedIn account

Link your personal LinkedIn account and post on it from Odoo.

Platform-specific post scheduling

Schedule a post at a different time for each social platform.

Schedule first comment

Schedule a comment on your own posts right after posting.

## Spreadsheet

Bubble charts

Use bubble charts to display data with both quantitative and categorical values.

Calculated columns

It is now possible to add calculated columns in dynamic lists.

Calendar charts

Calendar charts have been added.

Cell value orientation

Change the orientation of cell values.

Charts

- Add annotations to charts to provide more additional context or explanations.

- Get chart suggestions from selected columns using the Data Analysis feature.

- Define the colors of pie chart slices.

- Define minimums and maximums for x and y axes to better highlight your data.

Column statistics

Access quick statistics per column from the "Data" menu.

Conditional formatting by date

Apply conditional formatting based on dates.

Curly brackets

Use curly brackets, i.e., , to create arrays or as a custom formula parser.

Custom format

Define custom formats for dates & numbers.

Data cleanup

Remove unused data sources using the "Data Cleanup" menu.

Disable automatic recalculation

Disable the automatic recalculation of formulas and manually recalculate them by pressing F9.

Global filters

- Set default operators for global filters.

- Create global filters from the properties of a data source by directly selecting the field to filter on.

List insertion

Insert a dynamic list directly from a spreadsheet.

Lock sheets

Lock entire sheets to prevent modifications.

Mobile: pinch to zoom

Pinch to zoom in the grid when using Spreadsheet on mobile devices.

Multiple selection on figures

Select multiple figures, i.e., images, charts, or carousels, to reposition them at once, or select multiple charts to create a carousel.

Named range

Use the "Named Ranges" feature to add labels on cells or ranges. These named ranges can then be used in formulas or to better structure your spreadsheet.

New functions

New functions have been added:

- Lookup functions: CHOOSE, DROP, FORMULATEXT, and TAKE

- Statistical functions: CHISQ.TEST, CHISQ.TEST, CONFIDENCE.NORM, F.TEST, GEOMEAN, HARMEAN, KURT, SKEW, and TRIMMEAN

- Text functions: ARRAYTOTEXT

Pivot tables

- Add a "Domain" to a pivot table created from a range of spreadsheet data to filter the data by values or by criterion.

- Customize the appearance of pivot tables using the style options in the side panel.

- Apply specific styles to pivot tables.

- Expand and collapse all in dynamic pivots.

- In dynamic pivot tables, switch between the nested view and the tabular view, which flattens the row dimensions.

Print settings

Define print settings for a spreadsheet or dashboard and preview any changes in the new "Print Preview" window.

Property fields in inserted lists

Property fields visible in a list view are now included when inserting the list into an Odoo spreadsheet.

Reduced JSON file size

Downloaded JSON files have a new formula structure to reduce their overall size.

Regex formulas

The REGEXTEST and REGEXREPLACE formulas have been added.

Scientific notation

Format numbers in scientific notation.

Sheet background colors

Set a background color on an entire sheet grid.

Spill range operator

When using a spilled array function such as SEQUENCE, use the spilled range operator (#) to dynamically reference the entire spill range from a single cell.

Template access

Limit template visibility and access to defined user groups.

Top menu navigation

Use keyboard shortcuts to open the top menus, and navigate them using the keyboard's arrow keys and the Enter and Escape buttons.

Top/bottom ranking conditional formatting

The "Is in Top/Bottom ranking" conditional formatting option has been added, allowing cells to be formatted based on their ranking.

Zoom in/out

Zoom in or out to adjust the view of a spreadsheet.

## Studio

Automation rules: activity plans

Trigger activity plans through automations.

Form view customization

- Reorder a form view's buttons and smart buttons.

- Reorder tabs in a form view by dragging and dropping them.

Kanban stage cutomization

Customize Kanban stages (i.e., the status bar) from the form view using Studio.

List view column width

Define a minimum and maximum width for columns in the list view.

Many2Many fields in PDF reports

Many2Many fields can now be added to PDF reports via the field selector in the report editor.

Report editor 
- A new user interface has been introduced for the report editor.
- When editing a PDF report, a warning appears if the element is shared with other PDF reports.

Report translations

The report code can be translated in a single block that also displays the technical context.

User-friendly technical names

The technical name of the fields created with Studio are now more readable.

Warning for past or future dates

When adding a "Date" or "Datetime" field, use the "Warning" property to display a warning icon when a user enters a past or future date.

## Subscriptions

Loyalty programs

Create loyalty rules and rewards for subscriptions.

## Time Off

Calendar day vs working day deductions

The amount of time off taken can now be calculated based on either working days or calendar days.

Minimal amount for time-off requests

Define a minimum amount of time when a time off is requested.

Overview: all employees

The Time Off overview now displays all employees, including those without active leave requests.

Prevent app use

Assign users the new "No" user right for Time Off to prevent them from accessing the application.

Public holidays

- The "Public Holiday" time type is now available by default for all localizations, allowing public holidays to be generated without manual configuration.
- Public holidays can now be loaded for the year, with holidays corresponding to the companys country automatically suggested.

## Timesheets

ActivityWatch integration

Automatically track your device activity and turn it into timesheets while keeping your data private.

Log timesheets from anywhere

Use the timer in the top menu to track your time and log timesheets from anywhere.

Timesheet assistant: sharing rules

Share timesheet assistant rules with everyone, selected users or user groups, or keep them private.

Timesheet assistant: side activities

Configure a timesheet assistant rule as a "Side Activity" to track quick interruptions, such as checking messages or notifications, without affecting your main timeline.

Timesheet assistant: time thresholds

Set a threshold for each timesheet assistant rule so activities only appear in suggestions after reaching this duration.

## Website

Accented characters in URLs

Website URLs now support Unicode normalization, allowing the use of accented characters.

Age verification popup

Configure a popup to ask a website visitor to verify their age; access to the website is blocked if the minimum age is not respected.

AI Website Assistant

Multiple improvements have been made to the AI Website Assistant:

- The AI Website Assistant can help you vibe code your website by choosing and editing snippets, adding images from Unsplash or generating them, retrieving content on the web, and more.

- Ask the AI Website Assistant to create new webpages.

- When creating or editing a webpage, ask the AI Website Assistant to draw inspiration from other websites, use your database's records, such as products or events, or add interactive elements, such as counters, toggles, tabs, or animations.

- Ask the AI Website Assistant to create or edit webforms.

- Ask the AI Website Assistant to make changes to your menu, such as creating, renaming, moving/nesting, reordering, or deleting menu items.

- Use the "Select Elements" option to tell the AI Website Assistant which block you are commenting on.

- The AI Website assistant is now better at creating original designs and uses a more diverse range of blocks.
- Provide a URL to the AI Website Assistant and ask it to replicate that page on your website.

AI-generated content indicator

Building blocks that have been generated or modified by the AI Website Assistant are indicated in the Website editor.

Animated number building block

Use the "Animated Number" building block to highlight key metrics such as sales figures, customer counts, or performance indicators.

Appointment page layout

Choose whether the Appointment page displays appointments as a list, using cards, or using pictures.

Banners

Add a banner above a website's header to inform visitors about promotions, holidays, etc.

Blurred headers

Apply a blur effect on transparent background headers.

Breadcrumbs on static pages

Add breadcrumbs on static web pages to help users browse the website.

Card and column anchors

Add anchors to cards and columns for easier navigation, especially on mobile devices.

Card enhancements

- Set a card's animation to be triggered "On Hover."
- Make an entire card clickable.

Carousel transition

Control the transition speed of carousels.

Color palette preview

Preview the selected color palette on demo content.

Column content selection

Press CTRL + A inside a website column to instantly select its entire content.

Connection shape color

Connection shapes infer the correct background color from their context.

Cookies

- A "Close" button has been added to the cookie bar.

- Set any webpage as the cookie policy page.

Countdown snippets

Countdown snippets (both the block and the inner content) have been improved.

Customizable portal cards

Customize the cards on the portal homepage with the website editor.

DOM elements

Designers can now target DOM elements through namespaces. A class is added on body tags, specific to each page, e.g., o_website_sale_products on category product pages.

Donation snippet customization

Customize the "Donation" snippet to collect legally required information if needed.

Dropzones and overlays

Dropzones and overlays in the Website builder have been updated to be more modern and discrete.

Dynamic "Today" value

Date and datetime fields on Website forms now include adynamic "Today" value.

Dynamic carousels

Dynamic carousel building blocks are now available for all modules, including Blogs, Events, and Appointments.

Events, Jobs, and Blog pages

On Events, Jobs, and Blog pages where the sidebar is enabled, the "Follow Us" and "Share" sections have been replaced with standard "Social Media" and "Share" snippets that can be easily customized.

Font weight selector

Specify the weight for light, normal, and bold font weights.

Form enhancements

- Use the "no default value" option in a dropdown on a form to force visitors to select a value.

- More design options are available when adding a "Terms & Conditions" checkbox to forms.

- Set different border values for the top/bottom/left/right side in the "Theme" tab.

- Label positions are set at the form level.

- Restrict file upload to certain file types.

- Set character limits on text fields.

- Limit email submission to certain domains.

- Drag and drop inner content between a form's fields to create sections, add images, set titles, and more.

- Edit and format field labels directly in the form.

Forum notifications

Follow specific forum answers with fine-tuned notifications.

Fullscreen images

Click on images to see them full-size.

General shadow options

Set general shadow styles and options for sections, cards, etc.

Google Tag Manager (GTM)

Configure Google Analytics or Google Tag Manager from a single identifier field in the Website settings.

Inner content blocks: icons and Instagram

Add Icon and Instagram building blocks as inner content on website pages.

Job and eLearning course building blocks

Use dynamic building blocks (available in developer mode) to display jobs and eLearning courses.

Link and button styling

When the style of links or buttons is defined by the chosen theme, these can be modified in the "Theme" tab directly by clicking the pencil icon.

Link tracker: QR codes

Turn tracking links into QR codes for presentations and print.

llms.txt

Create an llms.txt file in the website settings to help large language models (LLMs) better understand and use website content.

Mega menu

Add blocks or inner content to a mega menu. A new, empty mega menu template has also been added.

Module-specific search

The main website search can now be configured to search either all content or only content within a specific module (e.g., Products, Events, etc.).

Multiple websites: change default site

Change the default website by reordering the websites in the configuration.

New Icon List inner content block

Add a list with icons using the new "Icon List" inner content block.

Partners page

Display members and partners online with a new module.

Portal users: profile picture

Portal users can now update their own profile picture from the portal dashboard.

Property fields supported in website forms

Property fields can be used in website forms when the action is "Create a Customer" or "Subscribe to Newsletter."

Publish/unpublish partners

Batch publish/unpublish partners.

Redirections

Redirection creation has been improved.

Repositioning text over cover image

Text over a cover image can now be repositioned by dragging and dropping.

Search results

- On a multi-language website, the page name is now translated in the search results.

- Search results are split by category with improved layouts and a new fuzzy search algorithm.

SEO

- Mobile performance has been improved via adaptive image sizing (srcset), and SEO logic has been refined to prevent duplicate content on paginated pages.

- Use AI to improve a web page's SEO.

Sidebar revamp

The Website sidebar now automatically folds secondary option groups to help you focus on your current selection.

Simplified inner content blocks

The "Social Media" and "Share" inner content blocks have been simplified by removing some options and merging others.

Social media links

Social media URLs are now handled in a uniform way in the "Social Media" inner content block.

Sitemap enhancements

Sitemaps include only indexable 2XX URLs and remain within Google size and URL limits.

Block preview for mobile devices

Preview how any block will appear on mobile devices.

Structured data

Structured data is now added to webpages' HTML by default to improve SEO and create Google Rich Results.

Theme layout and background options

Theme layout and background options have been clarified and unified.

Translation 

- Translate selection field options in contact forms more easily.

- On a multi-language website, static page URLs can now be translated per language.

- When translating web content, different images, videos, or documents can be displayed for each language.

Vertical video format

Add videos in vertical format.

Visitor tracking

The efficiency of the visitor tracking system has been improved.

Website configuration wizard

- The website configuration wizard includes an improved industry list and synonym matching, as well as AI-powered choices for positioning (e.g., luxury versus eco-conscious) based on the type of business.

- Several new themes have been added, existing themes have been improved, and default images for all themes have been updated.

Website editor UI/UX

The Website editor's UI and UX has been improved.

WhatsApp widget snippet

Allow visitors to start a WhatsApp conversation with your company in a single click.

## WhatsApp

Default recipents on templates

Add default recipients on WhatsApp templates.

Interactive message templates

Create interactive message templates.

Named parameters in templates

Use named parameters in WhatsApp templates.

Number blocking

Block a WhatsApp number directly from Odoo.

Simplified authentication process

The process to authenticate a WhatsApp business account has been simplified.

## 

### Odoo Experience on YouTube

 1. Use the live chat to ask your questions.

 2. The operator answers within a few minutes.

 Watch now
