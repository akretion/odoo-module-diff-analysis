# stock_account migration guide (19.0 -> 20.0)

In 20.0 the inventory valuation and closing logic has been **moved out of `stock_account` into the base Accounting app**, so valuation now exists even without the Inventory app. `stock_account` keeps what is stock-specific: perpetual (real-time) entries, category-level stock account defaults, and location reclassification entries.

## What's new for users

- **Valuation without Inventory**: you can follow stock on hand and its accounting valuation without installing the Inventory app.
- **Inventory valuation closing report**: in perpetual accounting, accrual entries (Bills To Receive, Billed Not Received, Invoices To Be Issued, Invoiced Not Delivered) can be posted directly from the closing report. They hit the inventory valuation account directly instead of transiting through a stock variation account.
- **COGS update**: when a product cost changes after delivery (landed cost, bill price differing from purchase price), the delivery value — and the invoice cost under perpetual accounting — is updated retroactively.
- **Valuation Adjustments**: cost adjustments and their valuation impact appear in a new debug-mode "Valuation Adjustments" menu; under perpetual accounting the journal entries are created automatically.
- Accounting-wide: account groups are replaced by **parent accounts** and account codes become optional, which changes how your stock accounts are organised.

## Technical data model changes

Removed fields:
- `account.move.inventory_closing`; closing moves are now traced through `closing_datetime`.
- `product.template.valuation`, `product.category.property_valuation`, `res.company.inventory_valuation` — the Periodic / Perpetual switch is no longer carried here.
- `res.company.inventory_period`, `account_stock_journal_id`, `account_stock_valuation_id`.
- `product.category.property_stock_journal`, `property_stock_valuation_account_id`, `property_price_difference_account_id`, `account_stock_variation_id` (and their `_active` fields).
- `account.account.account_stock_variation_id`, `account_stock_expense_id` (and `_active`); that model file is gone.

Removed methods: `_get_gross_unit_price()`, `_get_product_accounts()`, `get_product_accounts()`, `_get_price_diff_account()`, and on `res.company`: `action_close_stock_valuation`, `_action_close_stock_valuation`, `_cron_post_stock_valuation`, `_get_stock_valuation_account_vals`, `_get_continental_realtime_variation_vals`, `_prepare_inventory_aml_vals`, `stock_accounting_value`, `_get_accounts_by_product`, `_get_extra_balance`, `_get_valuation_product_domain`.

Changed:
- `cost_method` on `res.company`, `product.template` and `product.category` is no longer computed from the category/company cascade. It simply extends the Accounting selection with `fifo` (`ondelete='set default'`); `standard` and `average` come from Accounting.
- The chart template no longer auto-creates the "Inventory Valuation" (STJ) journal.

Renamed / new hooks:
- `stock_value()` → `get_inventory_value()` (real valuation instead of a `qty_available * standard_price` approximation).
- New: `_get_inventory_valuation_products(date)`, `_get_extra_closing_aml_vals(at_date)`, `_get_closing_move_extra_vals(at_date)`, `_get_closing_date_field()`.
- `_get_last_closing_date()` now reads the closing move's `closing_datetime`.
- Kept: `_get_location_valuation_vals()`. `account.move` gains `_update_qty_available()`.

## How your habits should change

- Configure valuation and closing in **Accounting**, not in Inventory: journal, valuation account and closing period are no longer per-company stock settings of this addon.
- The Periodic / Perpetual choice is no longer a `stock_account` category field — review your setup after migration, especially if you used category-level overrides.
- Closing entries are found through `closing_datetime`, not the old `inventory_closing` flag: saved filters and searches on that flag must be rebuilt.
- Cost method is now one shared setting between Accounting and Inventory. FIFO remains available, but the "category → company default" cascade is gone, so check each category.
- Custom modules, reports or filters touching the removed fields/methods (variation and expense accounts, `action_close_stock_valuation`, `stock_value`) need adaptation.

## What you gain by migrating

- One single valuation and closing engine shared with Accounting: simpler setup, fewer duplicated settings, and no need for the Inventory app to value stock.
- Cleaner closings: accrual entries posted at the source, hitting the valuation account directly.
- Retroactive COGS and value corrections when costs change after delivery, keeping margins and inventory value accurate.
