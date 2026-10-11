# pos_bancontact_pay migration guide (19.0 -> 20.0)

## What's new for users

Odoo 20.0 ships no release-note extract matching this addon, so everything below is taken from the module's own changes between 19.0 and 20.0.

The visible change is the Bancontact QR code: following the rebranding of Payconiq to Bancontact, the QR code design has been updated. The payment request now returns the QR code as an SVG, and the Point of Sale displays it inside the frames provided by Bancontact rather than as a plain QR image. The result looks like the official Bancontact material your customers already recognise.

The static sticker flow is also handled differently: the sticker image is now produced by Odoo on the fly, always at Bancontact's extra-large (XL) size, instead of being read from a URL computed and stored on the payment method.

Finally, payment requests for an amount of 0 are no longer sent: Bancontact Pay answers with an error in that case, so the request is avoided.

## Technical data model changes

Two fields are removed from `pos.payment.method` (2 deletions, 0 additions):

- `bancontact_sticker_size`: Selection with values S (180x180), M (250x250), L (400x400), XL (800x800), `required=True`, default `S`.
- `bancontact_sticker_url`: Char, computed and stored by `_compute_bancontact_sticker_url`.

The `_auto_init` override no longer creates the two matching SQL columns; `bancontact_usage` is still created, and `_load_pos_data_fields` still loads only `bancontact_usage` into the POS.

The compute method `_compute_bancontact_sticker_url` is gone. It is replaced by a new helper, `_fetch_bancontact_sticker_image()`, which calls Bancontact's QR endpoint with fixed parameters (`f=PNG`, `s=XL`) and returns the raw image content. It raises a `ValidationError` when the payment method does not use the `bancontact_pay` provider, when its usage is not `sticker`, or when `bancontact_ppid` is empty.

The sticker action returns an `ir.actions.act_url` whose URL is now the Odoo route `/bancontact_pay/sticker/<id>` with `target="new"` (previously the stored sticker URL with `target="download"`).

On the payment side, the payload sent to Bancontact's `/v3/payments` and `/v3/payments/pos` endpoints now includes a `reference` built from the order UUID with dashes removed and truncated to 35 characters. The QR code link returned by Bancontact is suffixed with `&f=SVG` so the POS receives a vector image.

## How your habits should change

- The "Sticker Size" field is gone. If you were used to choosing Small, Medium, Large or Extra Large, that choice no longer exists: stickers are always generated at extra-large size.
- The sticker button opens the sticker in a new browser tab instead of downloading it directly. From there you can save or print it as before.
- Prerequisites for the sticker stay the same: a Bancontact Pay payment method, the "Static Sticker" usage and a Bancontact PPID. If one of them is missing, Odoo now shows a clear error message instead of silently producing a broken URL.
- Your API key, PPID, test-mode and currency settings are unchanged. No manual data cleanup is needed: the two removed columns are simply no longer created.
- Nothing changes for classic QR-on-screen payments, except the improved Bancontact-branded rendering.

## What you gain by migrating

- A QR code and sticker design that match the current Bancontact branding, which reduces confusion at the counter after the Payconiq rebranding.
- Sharper, scalable QR codes on screen thanks to the SVG format.
- Sticker generation fully handled by Odoo: no stale stored URL, no risk of a sticker pointing at an outdated size or configuration.
- Fewer failed transactions, because zero-amount payment requests are no longer sent to Bancontact.
- Better traceability on Bancontact's side through the new payment reference carried by every payment request.
- Explicit error messages during setup, so misconfigured payment methods are caught before going live.
