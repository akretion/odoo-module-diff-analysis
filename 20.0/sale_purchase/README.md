# sale_purchase migration guide (19.0 -> 20.0)

This addon links Sales and Purchase so that selling a service can automatically generate a request for quotation (RFQ) to your vendor. In 20.0 that behaviour is no longer a separate switch: it becomes one of the standard "Create on Order" service options. The change is small on paper but it touches a field you may have used on product forms and in filters, so plan a short configuration review before go-live.

## What's new for users

The official 20.0 release notes (Sales, Purchase, Accounting, Inventory, eCommerce, CRM, Rental, Point of Sale) do not describe this change: the sale_purchase rework is not listed there. The functional improvement below comes from the addon itself (odoo/odoo#235670, "make subcontract service option easier to discover").

- Configuring a service to generate a subcontracting RFQ is now done in the **General Information** tab of the product, in the **Create on Order** selection field, as a new option named **Subcontracting RFQ**. Previously the equivalent setting ("Subcontract Service") was a checkbox hidden in the **Purchase** tab.
- The option therefore now sits alongside the other "create on order" behaviours (task, project, etc.), which makes it visible and consistent with the rest of the service configuration.
- The tooltip is preserved: selling this product on a sales order automatically creates an RFQ to buy it, and you should set a vendor on the product.
- Automatic safeguards: the option becomes unavailable (reverts to **Nothing**) if the product is not purchase-enabled or if its **Reinvoice Policy** is not "Ordered quantities"/"Delivered quantities" as required; and enabling it automatically makes the product purchasable.

## Technical data model changes

Removed:

- `product.template.service_to_purchase` (Boolean, company-dependent, non-copied) — **deleted**.

Added / changed:

- `product.template.service_tracking`: a new selection value `('subcontract', 'Subcontracting RFQ')` is added, with `ondelete={'subcontract': 'set default'}`.
- `_compute_purchase_ok`: forces `purchase_ok = True` on products using `service_tracking = 'subcontract'`.
- `_compute_service_tracking`: resets `service_tracking` to `'no'` when the template is not purchasable or its reinvoice policy is `'no'`.
- `_get_saleable_tracking_types()` and `_get_product_types_allow_zero_price()` now include `'subcontract'`.
- `_prepare_service_tracking_tooltip()` returns the subcontract RFQ help text.
- `_check_vendor_for_service_to_purchase()` renamed to `_check_vendor_for_service_tracking_subcontract()`; still raises a `ValidationError` ("Please define the vendor…") when no vendor is set.
- The old constraint `_check_service_to_purchase` and the onchange `_onchange_service_to_purchase` are gone: validation now runs in `create()` and `write()` over templates with `service_tracking == 'subcontract'` (note: `write()` now also validates, which was not the case before).
- `sale.order._purchase_service_create` notification search: `product_id.service_to_purchase = True` replaced by `product_id.service_tracking = 'subcontract'`, **plus** a new `company_id in sale_order.company_ids` filter (multi-company correctness).
- `sale.order.line` logic (`_onchange_service_product_uom_qty`, quantity-change handling, `_purchase_service_create` regeneration) now keys on `service_tracking == 'subcontract'`.

There is an official upgrade script (odoo/upgrade#8850) that maps existing `service_to_purchase = True` products to the new option.

## How your habits should change

- Stop looking for a "Subcontract Service" checkbox in the Purchase tab: configure the service through **Create on Order → Subcontracting RFQ** in General Information.
- After migration, spot-check your subcontract services: each one should show the new option and still have a vendor. A product with no vendor is blocked by validation on save, as before.
- Products sold as subcontract services must stay purchasable and keep a reinvoice policy; if you remove purchase or set reinvoice to "Nothing", Odoo silently resets the option to no automatic creation.
- Any saved filters, exports, automations or reports built on the `service_to_purchase` field must be rebuilt on `service_tracking = 'subcontract'`; the old field no longer exists.
- Multi-company users get more predictable behaviour: RFQ follow-up notifications are now restricted to the companies involved on the sales order.

## What you gain by migrating

- One single, discoverable place to configure all "create on order" service behaviours, instead of a purchase-tab checkbox duplicated nowhere else.
- Fewer misconfigurations, thanks to automatic coherence rules between purchase, reinvoice policy and service tracking.
