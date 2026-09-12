# stock_account migration guide (15.0 -> 16.0)

## What's new for users

The official 16.0 release notes contain **no stock_account-specific entry**: nothing new to configure for inventory valuation or Anglo-Saxon accounting, no new menu, report or setting in this addon. All the features listed in the Accounting notes (assets, payment terms, bank reconciliation, OCR, lock dates, localizations, spreadsheets, ...) belong to other addons and are out of scope here.

The real change behind stock_account is invisible in the interface: the invoice ↔ journal-entry synchronization engine was rewritten (computed fields instead of `onchange`), and stock_account was adapted to it. In practice, under Anglo-Saxon accounting, the cost-of-goods-sold (COGS) and interim lines are still generated automatically when posting a customer invoice or a vendor bill, and are still removed when you reset the entry to draft or cancel it.

## Technical data model changes

* **Field removed:** `account.move.line.is_anglo_saxon_line` (Boolean). It is replaced by the standard `display_type` field with the new value `cogs`.
* COGS/interim lines are now created with `display_type = 'cogs'` and `tax_ids = []`; the old `exclude_from_invoice_tab = True` flag is dropped (implied by `display_type`).
* Amounts of those lines are no longer computed directly as `debit`/`credit` from a balance: only `amount_currency` is set (signed), and `debit`/`credit` are derived by the accounting engine.
* Method changes:
  * `AccountMove._get_lines_onchange_currency()` now filters on `display_type != 'cogs'`.
  * `AccountMove._reverse_move_vals()` override was **removed** (anglo-saxon lines are no longer filtered there).
  * `AccountMove.copy_data()` filters on `display_type != 'cogs'`.
  * `button_draft()` / `button_cancel()` still unlink the generated COGS lines, now testing `display_type == 'cogs'`.
  * `AccountMoveLine._get_computed_account()` (old-style computed-field method) is replaced by a new-style `_compute_account_id()` override: storable-product lines of vendor bills still default to the stock input account, but the whole account computation runs first.
  * `_stock_account_get_anglo_saxon_price_unit()` locates the original line through `display_type == 'cogs'`.
  * New `@api.onchange('product_id') _inverse_product_id()` override, excluding COGS lines so that changing a product never turns one of them into a regular invoice line.

## How your habits should change

* Day-to-day encoding in the standard forms is unchanged.
* Anything referencing `is_anglo_saxon_line` must be updated to `display_type == 'cogs'`: saved filters, exports, server actions, Studio fields, print reports, or third-party/OCA modules. They will fail otherwise.
* Credit notes / reversals: the previous override that stripped anglo-saxon lines when not cancelling no longer exists; the behaviour is now handled by the generic accounting engine. Re-test your reversal flows.
* Custom modules overriding `_get_computed_account` must be ported to `_compute_account_id`.
* Editing COGS/interim lines manually makes even less sense than before (taxes forced empty, amounts driven by the engine).

## What you gain by migrating

* One declarative way to identify COGS lines (`display_type`), consistent with other special lines, instead of a stock-only boolean — simpler and safer customizations.
* Much better performance on large invoices: the underlying refactor took a 500-line invoice from ~2 minutes to ~4 seconds to create; this benefits stock/anglo-saxon invoice flows too.
* Less custom glue code and fewer override conflicts with community modules, as the model is now fully computed/declarative.

*Assessment based on PR odoo/odoo#96134 (Odoo 16.0, task-2711317).*
