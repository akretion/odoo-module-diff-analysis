# purchase migration guide (17.0 -> 18.0)

## What's new for users

The headline change for the Purchase app in 18.0 is **PO/Bill matching** (listed under Accounting in the release notes). A vendor bill no longer has to be linked to a purchase order when it is created — exactly what happens with EDI- or OCR-imported bills.

- A **Purchase Matching** screen, reachable from a smart button on a vendor bill, on a purchase order, or from the vendor contact form, lists PO lines still to be billed and bill lines not yet linked. All unmatched lines for that vendor are shown, so you work in one place.
- **Match**: select bill line(s) and the matching PO line(s). Several bill lines can be matched to the same PO line when they share the same product. Matching is refused when two or more PO lines carry the same product.
- **Add to PO**: push bill lines onto an existing open PO, or create a new PO from them — handy when a receipt must be created out of an invoice.
- **Add Down Payments**: a wizard creates a down-payment line on the PO and marks the paid deposit as invoiced. When the final bill arrives with the negative down-payment line, matching it clears the deposit and the PO becomes fully billed.

The release notes also mention accepting/declining RFQs from email or the portal. The other Purchase bullets (company-currency totals, lot numbers on receipts) and the "new purchase agreement type" (Purchase Agreements module) are not changes brought by this addon.

## Technical data model changes

A large refactor (shared `account.order.mixin` / `account.order.line.mixin`, PO down payments enabled by a settings checkbox) was merged during 18.0 development and then **fully reverted**: no mixins, no settings checkbox, and internal method names such as `_prepare_account_move_line` are unchanged. Bill & PO matching was added afterwards and is what remains.

New models
- `purchase.bill.line.match`: non-stored SQL view (`_auto = False`) unioning open PO lines and unlinked vendor bill lines. Fields: pol_id, aml_id, company_id, partner_id, product_id, line_qty, line_uom_id, qty_invoiced, purchase_order_id, account_move_id, line_amount_untaxed, currency_id, state, plus computed product_uom_qty, billed_amount_untaxed, purchase_amount_untaxed, reference.
- `bill.to.po.wizard`: the Add to PO / Add Down Payment dialog.

Changed models
- `purchase.order`: new `partner_bill_count` (related to partner_id.supplier_invoice_count), `action_bill_matching()`, `_prepare_down_payment_section_values()`, `_create_downpayments()`.
- `purchase.order.line`: new `is_downpayment` boolean; the `accountable_required_fields` SQL constraint now accepts a down-payment line without product/UoM/date.
- `account.move`: new computed `purchase_order_name` and `is_purchase_matched`, plus `action_purchase_matching()`.
- `account.move.line`: new `is_downpayment` boolean; `purchase_line_id` is now `copy=False`; new `_prepare_line_values_for_purchase()`.
- `res.partner`: new `action_open_purchase_matching()`; `supplier_invoice_count` is now filtered per company.

## How your habits should change

- Stop forcing the "bill created from the PO" flow for outside bills. Create the bill first, link it afterwards from Purchase Matching.
- Use the bill's smart button to check completeness: `is_purchase_matched` is false as soon as one product line has no PO line.
- Create PO down payments from the bill side (Add to PO > Add Down Payment), not from a settings option — the deprecated checkbox was reverted.
- When several PO lines share the same product, expect the match to be refused: the tool does not disambiguate yet.
- Vendor bill counts on contacts are now per company, so multi-company users may see different figures than in 17.0.

## What you gain by migrating

- **Reconcile automatically imported bills in seconds** instead of rekeying purchase orders — the main pain point of EDI/OCR bill flows.
- **One screen per vendor** for all pending matches, instead of hunting through orders and bills.
- **More reliable "Waiting Bills / Fully Billed" status** on purchase orders.
- **Deposits tracked on the PO**, so the final bill nets off the down payment and the order closes properly.
- **No data migration work**: because the mixin refactor was reverted, existing fields, views and method names stay where they were.
