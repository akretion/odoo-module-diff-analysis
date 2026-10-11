# delivery migration guide (19.0 -> 20.0)

This guide covers what changes for the **Delivery** app (`delivery`) when moving from Odoo 19.0 to 20.0. In short: the carrier and shipping logic you use every day is unchanged, while the **pickup point** feature leaves `delivery` and is now hosted by `website_sale_stock`.

## What's new for users

The official 20.0 release notes contain **no dedicated section for the Delivery app**: no new carrier, no change to delivery methods, delivery costs, delivery lines or shipping weight rules. The only user-visible news concern pickup points and are listed under eCommerce:

- **Country filter in the pickup point selector** – customers can filter pickup points by country in the selection dialog.
- **Pickup address can be changed from the backend** – sales and delivery orders use the same location selector as eCommerce, so a pickup point can be changed without going back to the website.
- **Mondial Relay via Sendcloud** – the separate Mondial Relay pickup point module for eCommerce has been removed; Mondial Relay remains available, alongside other pickup points, through Sendcloud.

Everything else you know from 19.0 (carriers, carrier selection wizard, delivery lines, shipping weight computation) keeps working the same way for functional users.

## Technical data model changes

The pickup point feature was moved out of `delivery` because every delivery method supporting it already depends on `website_sale_stock`. This is a clearing-out commit: nothing is added to `delivery` itself.

**Removed fields**
- `res.partner.is_pickup_location` (Boolean) – used to flag an address as a pickup point.
- `sale.order.pickup_location_data` (Json) – stored the raw pickup point payload on the order.

**Removed methods / overrides**
- `res.partner._get_delivery_address_domain()` – the delivery address filter no longer excludes pickup points.
- `sale.order._compute_partner_shipping_id()` – selecting a pickup point no longer resets the delivery address back to the customer.
- `sale.order._set_pickup_location()` and `sale.order._get_pickup_locations()` – location retrieval and storage now live elsewhere.
- `sale.order._action_confirm()` – `delivery` no longer creates the pickup address on order confirmation.

**Replacement behaviour (now in `website_sale_stock`)**
- A **new Json field on `res.partner`** captures the information the shipping provider needs when processing the shipment.
- On confirming an order with a pickup delivery, a **real delivery address contact is created** from the point data, or an existing identical address is reused.

Breaking score of the change: 2 field deletions, 0 additions. No new model and no signature change on the remaining `delivery` methods.

## How your habits should change

- **Look at the shipping address, not a JSON blob.** The pickup point is now a genuine contact linked to the order, so it appears on documents, the portal and in Contacts like any other delivery address.
- **Pickup points no longer disappear from address lists.** The delivery address selector follows standard rules again: a point is just another delivery address.
- **Selecting a point no longer resets your delivery address** to the customer; the order simply points to the pickup address.
- **Manage duplicate pickup addresses through contacts.** Identical point data reuses the existing address instead of creating a new one.
- **Change a pickup point from the sales or delivery order** using the new location selector, instead of only from the eCommerce flow.
- **Adapt custom developments.** Any customization referencing `is_pickup_location`, `pickup_location_data`, or the removed methods must be reworked against the new `res.partner` field before upgrading.
- Address cleanup is handled by the standard upgrade scripts, but review your pickup contacts after the migration.

## What you gain by migrating

- **Cleaner, reusable data**: pickup point information is attached to the partner, not buried in a per-order JSON field.
- **Consistent documents**: orders, invoices and portal pages show the real pickup address.
- **Less contact clutter**: identical pickup addresses are detected and reused.
- **Backend flexibility**: pickup points can now be chosen or changed directly on sales and delivery orders.
- **Better carrier hand-off**: shipping providers retrieve the data they need from a single, standard partner field.
- **A supported upgrade path**: the removal is intentional and covered by the official upgrade scripts, so staying on 19.0 only delays a change that is already handled for you.

For a Community deployment, the practical impact is limited: carriers, delivery costs and delivery slips behave as before, and only the pickup-point configuration and your custom code need attention.
