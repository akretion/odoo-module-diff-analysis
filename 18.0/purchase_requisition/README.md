# purchase_requisition migration guide (17.0 -> 18.0)

This guide summarizes what changed in the `purchase_requisition` addon between Odoo 17.0 and 18.0 for functional users, and what to prepare before migrating to Community 18.0.

## What's new for users

**A second agreement type: Purchase Template.** Agreements are no longer configured through a separate "Agreement Type" model. Odoo 18 offers exactly two hard-coded types, chosen directly on the agreement:
- **Blanket Order**: negotiated quantities/prices over a period, with supplier info created (historic behaviour).
- **Purchase Template**: create RFQs with fixed quantities easily; unit prices are pre-filled from the vendor pricelist, and no supplier info is created.

**Simpler agreement states.** The status list is reduced to Draft, Confirmed, Closed, Cancelled. The former Ongoing / In progress / Bid selection states disappear — "Confirmed" now covers both agreement types. Behaviour change: confirming an RFQ linked to an "exclusive" agreement no longer cancels the other RFQs and closes the agreement automatically.

**Vendor currency is now automatic.** The agreement currency follows the vendor's purchase currency (falling back to the company currency); no more manual onchange.

**Purchase alternatives are opt-in.** Comparing alternative RFQs (and merging them) is now hidden by default and enabled through the new Purchase setting "Purchase Alternatives".

**Company-currency totals.** Purchase order and order line totals are available in company currency, so alternatives can be compared in a single currency.

## Technical data model changes

Removed model: `purchase.requisition.type` (agreement types are now a selection).

`purchase.requisition`
- Removed: `type_id`, `is_quantity_copy`, `state_blanket_order`, `origin` (renamed `reference`), `ordering_date`, `schedule_date`.
- Added: `active`, `reference`, `requisition_type` (blanket_order / purchase_template, required, default blanket_order), `date_start` (Date), `date_end` (now Date, was Datetime).
- `state` selection reduced to draft/confirmed/done/cancel; `name` comes from a dedicated sequence per type and is re-generated when type/company changes (draft only); currency is now computed, stored and editable from the vendor.
- New constraint `_check_dates`: end date cannot be earlier than start date.
- Methods removed: `_get_type_id`, `_set_state`, `action_in_progress`, `action_open`. Added: `action_confirm`, `create`, `write`, `_compute_currency_id`, `_check_dates`.

`purchase.requisition.line`
- Removed `schedule_date`; `price_unit` becomes computed (vendor pricelist), stored, editable; labels of `qty_ordered` and `product_description_variants` shortened.
- `_onchange_product_id` and `create_supplier_info` removed; `_compute_price_unit` and `_create_supplier_info` added.

`purchase.order`
- `requisition_id` label: "Blanket Order" -> "Agreement"; `is_quantity_copy` replaced by related `requisition_type`; added `_merge_alternative_po`, `_prepare_grouped_data`.

`purchase.order.line`
- Added `_compute_price_total_cc` (company currency total).

New file/model: `res.config.settings.group_purchase_alternatives`.

## How your habits should change

- Choose "Blanket Order" or "Purchase Template" on the agreement itself; the agreement-type menu is gone (menus are now named "Agreement Types").
- Use Start Date / End Date fields; per-line scheduling has moved to purchase orders.
- Use Confirm / Close actions; expect "Confirmed" instead of Ongoing / In progress / Bid selection.
- Select the vendor first, then check the currency — it is derived from the vendor.
- Enable the Purchase Alternatives setting if your team relies on comparing or merging RFQs.
- Existing records are converted by Odoo's upgrade scripts; review in-flight agreements after migration since removed states and dates are remapped.

## What you gain by migrating

- One clear, simpler model covering both blanket orders and purchase templates, with cleaner naming and fewer states.
- Faster RFQ creation from purchase templates, with prices pre-filled from vendor pricelists.
- Fewer manual steps: automatic currency, sequenced references, and start/end date validation.
- Better comparison of competing RFQs thanks to company-currency totals and the optional alternatives workspace.
