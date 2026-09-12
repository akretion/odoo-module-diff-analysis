# lunch migration guide (12.0 -> 13.0)

## What's new for users

The Odoo 13.0 release notes for Lunch highlight three user-facing changes that this addon really implements:

- **Vendor availability management.** Availability is now defined per vendor (one specific day, or recurrent weekdays, with a from/to time range and a timezone) instead of free-text "lunch alerts". It tells users what can be ordered on a given day.
- **A revamped ordering interface.** Ordering is simplified with a new kanban-style screen ("à la Documents"): you pick products, extras (toppings) and quantities instead of typing order lines, with product images.
- **Emails to the vendor.** The order of the day can be sent by email directly to the vendor – automatically at a configured hour, or manually.

The release note about a notification "in the chatter or in the app" is too generic and not specific to this addon; it is not detailed here.

## Technical data model changes

**New model – `lunch.supplier`** (Lunch Supplier): linked `res.partner` with related contact/image fields, `send_by` (Phone/Email), `automatic_email_send` + `automatic_email_time`, `tz`, and availability fields (`recurrency`, `recurrency_from`/`_to`, `recurrency_date`, weekday booleans, `available_today` computed and searchable). A cron (`_auto_email_send`) emails pending orders to suppliers.

**Removed model – `lunch.alert`**, together with the `alerts` field on `lunch.order`. Vendor availability replaces it.

**`lunch.product`**
- `supplier` (res.partner) becomes `supplier_id` (lunch.supplier, required); `price` becomes required; `category_id` is no longer required; label "Product" → "Name".
- `available` (computed/searchable) is removed.
- Added: `is_topping` ("extra garniture"), `already_ordered`, `new_until`, `favorite_user_ids`, `company_id`, `currency_id` and images via `image.mixin` (`image`, `image_128`, `image_64`).

**`lunch.order`**
- `state` is no longer computed from the order lines: it defaults to New, gains an **Ordered** step and keeps Received / Cancelled.
- Added: `mail_sent`, `supplier_ids` (computed, stored). Removed: `alerts`, `previous_order_ids`, `previous_order_widget`.
- New methods: `action_order()`, `action_confirm(supplier=None)`, `action_cancel()`.

**`lunch.order.line`**
- `product_id` is filtered on `is_topping = False`; `supplier` becomes `supplier_id`.
- `price` is now a stored computed value: quantity × (product price + toppings), instead of the related product price.
- Added: `quantity` (default 1) and `topping_ids`; `cashmove` is now linked through `order_line_id`.
- New: `update_quantity(increment)` (checks the user's wallet balance), `action_confirm()`, `action_cancel()`; the old `order()`, `confirm()`, `cancel()` and create/write availability checks are removed.

**`lunch.cashmove`**: `order_id` is renamed `order_line_id`; new `get_wallet_balance(user)`.

No method signature changes were detected for this addon.

## How your habits should change

- **Vendors instead of alerts:** create a Lunch Supplier per vendor (partner, availability recurrence, email settings) rather than alert records – availability is now a property of the supplier.
- **Wallet timing:** the wallet is debited when the order is placed (one cash move per ordered line), not when the manager marks it as received. Changing a quantity updates the cash move; cancelling a line removes it.
- **Ordering:** use the new order screen with quantities and toppings; the app refuses any change exceeding the wallet balance.
- **Sending to vendors:** orders are grouped per supplier; send them by email manually, or let the cron do it for suppliers configured for automatic sending.

## What you gain by migrating

- Vendors are properly modelled (contact, availability, timezone, email) instead of being loose partners plus a separate alert list.
- Fewer mistakes: availability, quantities, toppings and wallet controls are enforced by the app, not by the manager's memory.
- Faster daily handling: automatic vendor emails replace the manual composer step.
- A richer catalogue: product images, toppings and favourites make ordering clearer for employees.
- You move to a supported version and to the 13.0 data model that later versions build upon.
