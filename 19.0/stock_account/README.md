# stock_account migration guide (18.0 → 19.0)

## What's new for users

Odoo 19.0 rebuilds inventory valuation in `stock_account`. The release notes confirm it: *"Inventory valuation has been simplified and new features have been added, including a new closing interface and support for transfer backdating."*

For companies using Automated or Manual valuation, Anglo-Saxon accounting, or Standard/FIFO/AVCO costing:

- **New closing interface**: a company-level action closes the stock valuation period and posts the entry (an automated scheduled action exists).
- **Backdated valuation**: values can be recomputed at a chosen date, so past periods can be re-examined.
- **Explainable move valuation**: Odoo shows how a move value was obtained (quotation, vendor bill, standard price, return, manual entry).
- **Manual value adjustment**: adjust a move's value directly, generating the matching accounting entry.
- **Lot/Serial valuation** is computed per lot, and is disabled when a product's tracking changes.
- **Locations**: simplified configuration (fewer default virtual locations); an explicit "is valued" flag is used.
- **Manufacturing**: for AVCO/FIFO, the work center labour cost is used when no employee cost is set, and operation costing feeds the finished product value.

Other 19.0 accounting highlights belong to other addons.

## Technical data model changes

- **New model `product.value`**: stores the details of a move valuation (`value_description`, `current_value_details`, `company_id`).
- **`stock.move`**: new computed fields `is_in`, `is_out`, `is_dropship`, `is_valued`, `remaining_qty`, `remaining_value`, `value_manual` (invertible), plus `action_adjust_valuation`, `_get_value`, `_get_value_data`, `_get_manual_value`, `_get
