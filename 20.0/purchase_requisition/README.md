# purchase_requisition migration guide (19.0 -> 20.0)

This addon covers Purchase Agreements (blanket orders / call for tenders) and the link between a Purchase Order and its agreement. Between 19.0 and 20.0 most of the change is a **scope split**: the "alternative POs" comparison feature has been extracted from `purchase_requisition` and now lives in its own dedicated module.

## What's new for users

- **Purchase agreement structure** — sections and notes can now be added inside purchase agreements / blanket orders, the same way they are used on purchase orders. This makes long agreements readable and easier to share with suppliers.
- **Alternatives comparison** — the release notes state the comparison screen has been reworked to make it simpler for users to pick the best offer. In 20.0 this screen is no longer shipped by `purchase_requisition`: the whole alternatives mechanism (create an alternative, compare lines, choose the best lines, confirm warnings) has been moved to a separate "purchase alternatives" module. If your team uses alternatives today, confirm with your integrator that this module is available and installed in your edition before you migrate.
- Nothing else in the 19.0 -> 20.0 Purchase release notes concerns the agreement/requisition flow itself (Incoterms, expected arrival dates, vendor quality rate, portal address, section totals on POs, etc. belong to other addons).

Everything else that `purchase_requisition` is known for is unchanged: agreements and blanket orders, call for tenders, the **Agreement** field and **Agreement Type** on purchase orders, automatic grouping of RFQs generated from an agreement, and the requisition-aware line naming/description logic.

## Technical data model changes

Removed from `purchase_requisition` (moved to the new alternatives module, not deleted from the product):

- Model `purchase.order.group` ("Technical model to group PO for call to tenders") and its `order_ids` One2many, plus its `write()` self-implode behaviour.
- On `purchase.order`:
  - `purchase_group_id` (Many2one -> `purchase.order.group`)
  - `alternative_po_ids` (One2many, related to `purchase_group_id.order_ids`)
- On `purchase.order.line`:
  - `price_total_cc` (stored Monetary, "Company Subtotal")
  - `company_currency_id` (related to `company_id.currency_id`)
- On `res.config.settings`: the whole `res_config_settings.py` file, i.e. the `group_purchase_alternatives` boolean and its `purchase_requisition.group_purchase_alternatives` implied group.

Method-level changes on `purchase.order` / `purchase.order.line` (all removed from this addon):

- `PurchaseOrder.button_confirm()` override (the "What about the alternative Requests for Quotations?" warning popup).
- `action_create_alternative()`, `action_compare_alternative_lines()`, `get_tender_best_lines()`, `_merge_alternative_po()`.
- The alternative-related blocks inside `create()` (origin PO linkage) and `write()` (group creation/merge/unlink).
- On `purchase.order.line`: `_compute_price_total_cc()`, `action_clear_quantities()`, `action_choose()`.

What is **added** in this addon by these commits: nothing new. What remains intact: the `requisition_id` / `requisition_type` fields and `_onchange_requisition_id()` on `purchase.order`, the requisition-aware `create()`/`write()` messaging, `_prepare_grouped_data()` adding `rfq.requisition_id.id` to the matching keys, and the requisition part of `_compute_price_unit_and_date_planned_and_name()`.

## How your habits should change

- **Using alternatives?** You now enable and use them through a separate module instead of the "Purchase Alternatives" checkbox in Purchase settings, and the group/licensing check should be done up front. The workflow itself (create alternative, compare lines, clear quantities, confirm) is preserved, with a redesigned comparison screen.
- **Data to check after the upgrade**: existing alternative-PO groupings previously stored in `purchase.order.group` must be carried over to the new module's structures. The commit references upgrade script `odoo/upgrade#8052` for this; always run the migration on a database copy and review draft RFQs that were linked as alternatives.
- **Reports/customisations**: any custom report, filter, export or automation referencing `alternative_po_ids`, `purchase_group_id`, `price_total_cc` or `company_currency_id` will break and must be pointed at the new module's fields.
- **Agreements**: you can now write sections and notes inside agreements, so structure long blanket orders instead of putting everything in line descriptions.

## What you gain by migrating

