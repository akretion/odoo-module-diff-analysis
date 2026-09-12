# purchase migration guide (17.0 -> 18.0)

## What's new for users

**PO / Bill matching (new screen).** With EDI and OCR-imported vendor bills becoming common, bills often arrive without a link to the original purchase order. Odoo 18 adds a **Purchase Matching** screen (smart button on the vendor bill and on the purchase order) listing all still-matchable bill lines and PO lines of a vendor. From there you can:
- manually **Match** a bill line to the corresponding PO line (several bill lines may point to the same PO line when they share the same product);
- **Add to PO**: turn invoiced products into lines of an existing PO, or into a brand-new PO;
- **Add Down Payment**: register a deposit line on an existing PO.

Typical deposit flow: a 10% down-payment bill (no product) becomes a down-payment line on the PO; later the negative down-payment line of the final bill is matched back to it, leaving the PO fully billed.

**RFQ accept / decline from email or the portal.** Vendors can now accept or decline a request for quotation from the email notification or from the portal, instead of only returning a reply outside Odoo.

**Amounts in local currency.** Purchase orders show totals in company currency alongside the order currency, so orders and vendor alternatives can be compared in one currency.

**Product catalog: "In Order" filter.** The catalog opened from a PO line can filter products that are already present in a purchase order.

## Technical data model changes

- **New model `purchase.bill.line.match`** — a non-stored SQL view (`_auto = False`) unioning open PO lines and unmatched vendor-bill lines, with `pol_id`, `aml_id`, `product_id`, `line_qty`, `qty_invoiced`, `purchase_order_id`, `account_move_id`, `line_amount_untaxed`, `state`, plus computed `reference`, `display_name`, `product_uom_qty`, `billed_amount_untaxed`, `purchase_amount_untaxed`.
- **`account.move`**: new computed `purchase_order_name` and `is_purchase_matched` (all product lines linked to a PO line), plus `action_purchase_matching`.
- **`account.move.line`**: new `is_downpayment`; `purchase_line_id` is no longer copied when duplicating a bill (`copy=False`).
- **`purchase.order`**: new `partner_bill_count`; new methods `action_bill_matching`, `_create_downpayments`, `_prepare_down_payment_section_values`, `action_add_from_catalog`, `action_merge`/`_merge_alternative_po`, `get_order_timezone`/`get_localized_date_planned`.
- **`purchase.order.line`**: new `is_downpayment`; the DB constraint now allows non-product down-payment lines; new `_get_select_sellers_params`, `_has_valid_qty_to_invoice`, `action_open_order`.
- **`res.partner`**: `action_open_purchase_matching`; supplier invoice counters are now filtered on the current company.
- **Signatures changed** (developer impact): `_default_order_line_values(child_field=False)`, `_get_product_catalog_record_lines(product_ids, child_field=False)`, `_get_product_catalog_lines_data(**kwargs)`, `_convert_to_tax_base_line_dict(**kwargs)`, `_notify_by_email_prepare_rendering_context(msg_vals=False)`, `account.tax._hook_compute_is_used(taxes_to_compute)`. `_confirm_reception_mail` is discontinued; use `confirm_reception_mail` / `decline_reception_mail`.
- **Reverted refactor worth knowing:** the `account.order(.line).mixin` introduced during 18.0 development (which moved many `purchase.order(.line)` fields into shared mixins with Sales) was reverted before release. `purchase.order` and `sale.order` stay independent and their field definitions are unchanged from 17.0.

## How your habits should change

- For EDI/OCR bills, stop recreating POs by hand: open **Purchase Matching** from the bill and link or create the PO lines.
- Record vendor deposits as **down-payment lines on the PO**, not as free-text lines, so the final bill can clear them.
- "Fully billed" now relies on line-level matching: a bill with unmatched product lines no longer appears linked.
- Expect vendors to confirm or decline RFQs themselves from their inbox/portal.
- Duplicating a vendor bill no longer keeps the purchase-order line link.

## What you gain by migrating

- End-to-end traceability between vendor bills (including EDI/OCR) and purchase orders, through a dedicated matching screen instead of guesswork.
- Clean down-payment handling on POs, with deposits automatically cleared against the final bill.
- Company-currency totals for comparing orders and vendors.
- Self-service RFQ answers from your vendors and a richer catalog filter for recurring purchases.
