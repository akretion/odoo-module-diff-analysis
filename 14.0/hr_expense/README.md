# hr_expense migration guide (13.0 -> 14.0)

This guide summarizes what Odoo 14.0 changes for the Expense application (`hr_expense`, Community edition), for users coming from 13.0.

## What's new for users

- **Dedicated menus and list views**: you now find all your expenses and all your expense reports in one place, and you can organize them with an optional list view instead of only form views.
- **Clearer status overview**: it is easier to see what still has to be submitted, what is waiting for approval, and what is scheduled for reimbursement.
- **Automatic filling**: description, unit price, unit of measure, taxes and expense account of an expense line are now filled and kept consistent automatically from the selected product, whenever the line is created or modified — including when it does not come from the form (import, email alias).
- **OCR (bill digitization) is Enterprise**: the OCR send-mode configuration no longer lives in the Expense settings; it moved to the dedicated `expense_extract` addon, part of Odoo Enterprise. Community users simply no longer see these options.

The official release notes for this version are fuzzy for Expenses and mostly cover Enterprise features; only the points above are visible in Community.

## Technical data model changes

**Removed / moved**
- `res.company.expense_extract_show_ocr_option_selection` removed; `hr_expense` no longer inherits `res.company`.
- `res.config.settings.expense_extract_show_ocr_option_selection` (related field) removed. `module_hr_expense_extract` remains.

**hr.expense**
- `name`, `unit_amount`, `product_uom_id`, `account_id` become stored computed fields (`_compute_from_product_id_company_id`, depending on `product_id`, `company_id`); they stay editable, and the description is only proposed when still empty.
- `tax_ids` is now computed stored (and still editable).
- `employee_id` is now computed stored (`_compute_employee_id`, depending on `company_id`) from the current user's employee.
- The onchange `_onchange_product_uom_id` is replaced by a Python constraint `_check_product_uom_category`.

**hr.expense.sheet**
- `address_id`, `user_id`, `department_id` become computed stored from the employee (`_compute_from_employee_id`); the Manager is read-only, address and department remain editable.

**product.template**
- `can_be_expensed` becomes computed stored (`_compute_can_be_expensed`, depending on `type`) and is forced to False for products that cannot be expensed; `_onchange_type_for_expense` is removed.

**res.config.settings**
- `expense_alias_prefix` becomes computed stored (`_compute_expense_alias_prefix`, depending on `use_mailgateway`) and is cleared when the mail gateway is disabled.

No method signature changes were detected.

## How your habits should change

- **Fewer onchange surprises**: these fields are now computed *and stored*, so they are filled or recalculated at creation and at every write, not only while editing the form. Change the product on a draft expense and the unit price, UoM, taxes and account are updated (an already typed description is kept).
- **Changing the employee on a report** updates the manager, the department and the home address automatically; you no longer set them by hand.
- **Unit of measure check on save**: selecting a UoM from another category now raises an error when you save the record, not while editing.
- **Imported or emailed expenses** get their default values from the product even without opening the form.
- **Expense settings are lighter**: the OCR send-mode selection is gone from the Expense configuration screen.

## What you gain by migrating

- Less manual entry and fewer mistakes: product, account, taxes, manager and department are derived automatically and consistently, whatever the entry point (form, import, email).
- Better visibility on expense and report statuses thanks to the dedicated menus and list views.
- A cleaner separation between the free Community expense workflow and the Enterprise OCR addon: your Community database no longer carries settings for a feature you do not own.
- A maintained version: 13.0 no longer receives the same level of fixes and improvements as 14.0.
