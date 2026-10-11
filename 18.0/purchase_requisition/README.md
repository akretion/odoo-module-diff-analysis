# purchase_requisition migration guide (17.0 -> 18.0)

This addon manages purchase agreements: blanket orders and the recurring agreements used to generate RFQs. 18.0 reshapes it noticeably — the configurable "agreement type" model disappears, one new agreement type arrives, and several fields and statuses are renamed or removed.

## What's new for users

The official 18.0 release notes are thin for this addon. Under Purchase, only one item concerns purchase agreements:

- **New purchase agreement type**: "Added a new type of purchase agreement to create requests for quotation with fixed quantities easily."

This is the **Purchase Template** agreement, which now sits next to the existing **Blanket Order**. The other items listed in the Purchase, Sales and Accounting release notes (company-currency totals, RFQ accept/decline from email or portal, lot-number generation on receipts, PO/bill matching) belong to other modules and do not change how agreements work.

One visible consequence for agreement users: the purchase alternatives feature is no longer displayed by default — a new **Purchase Alternatives** setting in Purchase settings must be enabled to use it.

## Technical data model changes

**Removed model**: `purchase.requisition.type` (fields `name`, `sequence`, `exclusive`, `quantity_copy`, `line_copy`, `active`). It is replaced by a simple selection.

**`purchase.requisition`**
- Added `requisition_type` selection (`blanket_order`, `purchase_template`, default `blanket_order`), `reference`, `active`, `date_start` (replaces `ordering_date`).
- `date_end` is now a Date (was Datetime); `origin`, `schedule_date`, `state_blanket_order`, `is_quantity_copy` and `type_id` are removed.
- `state` is reduced to `draft / confirmed / done / cancel`: `ongoing`, `in_progress` and `open` are gone. `action_in_progress` becomes `action_confirm`; `action_open` and `_set_state` are removed.
- Names are generated from `ir.sequence` at creation, with one sequence per agreement type.
- New `_check_dates` constraint refuses an end date earlier than the start date; changing the agreement type or company on a non-draft agreement raises a UserError; `currency_id` is now stored and computed from the vendor.
- `unlink` also removes draft lines.

**`purchase.order`**: the `requisition_id` label becomes "Agreement"; new related `requisition_type`; `is_quantity_copy` removed. `_onchange_requisition_id` no longer depends on `line_copy` and sets quantities to zero for purchase templates; `date_order` becomes `max(now, date_start)`. The old behaviour that cancelled other RFQs on confirmation of an "exclusive" agreement is removed. `has_alternatives` now requires the new `group_purchase_alternatives` group.

**`purchase.requisition.line`**: `schedule_date` and `_onchange_product_id` removed; `price_unit` is stored and computed from the vendor pricelist for purchase templates (still editable); `create_supplier_info` renamed `_create_supplier_info`; labels "Custom Description" → "Description" and "Ordered Quantities" → "Ordered".

**New**: `res.config.settings.group_purchase_alternatives`.

## How your habits should change

The release notes say nothing further specific to this addon, so the rest follows from the changes above:

- Pick the agreement type when you create the agreement: it drives numbering, available date fields and confirmation checks.
- Use **Confirmed** instead of the former Ongoing / In progress / Bid Selection statuses.
- Blanket orders still require a positive price and quantity per line before confirmation; purchase templates prefill prices from the vendor pricelist.
- If you relied on comparing RFQs, enable the alternative setting first.

## What you gain by migrating

- Two clear agreement types instead of a configurable type model to maintain.
- Automatic agreement numbering and start/end date validation.
- Vendor pricelist prices prefilled on purchase templates.
- A lighter default interface, with alternatives available on demand.
