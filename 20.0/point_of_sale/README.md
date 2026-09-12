# point_of_sale migration guide (19.0 -> 20.0)

## What's new for users

No official Odoo 20.0 release notes extract was available for this addon, so this section is derived from the code changes shipped in the Community `point_of_sale` module.

- **Printer setup has been rebuilt.** A single Printers form now handles every printer. You can attach several receipt printers to one POS, share a printer between POS configurations, and pick the default receipt printer directly from the POS interface — with a fallback printer offered when the chosen one is unreachable (no paper, lost connection...).
- **Printers are typed**: "Preparation" (kitchen, bar) or "Receipt".
- **Receipts are generated from a single shared template** usable by both the server (Python) and the POS (JavaScript). Receipts can be generated on demand and downloaded as PDF from the backend order, and a receipt can be printed again from the POS.
- **Payment methods were reworked**: the "Use a Payment Terminal" field becomes a general "Payment Provider", and a new "Quick Pay (QR Code)" method type supports QR-based electronic payment providers such as Bancontact Pay.
- **Test buttons** let you verify a given printer, or all printers of a POS configuration, before going live.

## Technical data model changes

**Removed models**
- `product.template.attribute.exclusion` (POS model): variant exclusions are now a plain many2many `excluded_value_ids` on `product.template.attribute.value`.
- `pos.printer.pending.receipt` (Epson Server Direct Print queue).

**Renamed fields on `pos.config`** (handled by the upgrade script)
- `is_order_printer` → `use_order_printer`, `is_posbox` → `use_posbox`, `is_header_or_footer` → `use_header_or_footer`, `is_closing_entry_by_product` → `use_closing_entry_by_product`, `is_kiosk_mode` → `use_kiosk_mode`.
- `auto_validate_terminal_payment` → `auto_validate_electronic_payment` (now covers terminals and QR codes).

**Removed fields**
- `pos.config`: `iface_electronic_scale`, `iface_print_via_proxy`, `iface_scan_via_proxy`, `proxy_ip`, `use_posbox`, `epson_printer_ip` (moved to `pos.printer`), `iface_cashdrawer`, `default_receipt_printer_id`.
- `pos.payment.method`: `use_payment_terminal` and `hide_use_payment_terminal`, replaced by `payment_provider` / `hide_payment_provider`.
- `pos.config.printer_ids` is replaced by `preparation_printer_ids` and `receipt_printer_ids`.

**New fields**
- `pos.printer`: `use_type` (preparation/receipt), `use_lna`, `use_cashdrawer` (takes over the cash-drawer setting).
- `pos.payment`: `qr_code`. `pos.config`: `preparation_devices`.
- `payment_method_type` gains `external_qr`; the former `qr_code` value is renamed `bank_qr_code`.

**Method signature changes**: the data-loading API was reworked — `_load_pos_data_domain(data)` no longer takes `config`, and `_load_pos_data_dependencies`, `_load_pos_metadata` and `load_data(local_data={})` were introduced. Session closing now uses `close_session_from_ui(payment_method_closing={})`, and `action_send_receipt(email)` no longer receives images (they are generated server-side). Rescue-session helpers were dropped.

## How your habits should change

- Configure printers **once** in the dedicated Printers menu instead of at two places, then select them per POS. The default receipt printer is no longer stored on the POS configuration: operators choose it in the POS, and can change it from the Printers section of the POS menu.
- The **cash drawer is now linked to a printer**, not to the POS configuration.
- If you relied on **Epson Server Direct Print**, it has been removed; plan an alternative printing setup.
- **IoT Box device settings are gone from Community** (print/scan via proxy, electronic scale, proxy IP, PosBox). Use the supported printer types instead.
- Bank-app QR payments are configured through the **new provider field**, and the old terminal checkbox no longer exists.
- Receipt e-mails and PDFs are generated from the new shared template; re-check your custom receipt layout if you had one.

## What you gain by migrating

- A simpler, unified printer configuration: multiple receipt printers, shared printers, front-end default and fallback selection — fewer support calls when a printer fails.
- Less duplicated code: one receipt template for Python and JavaScript, so custom receipts and localisation modules stay consistent, plus on-demand PDF download and reprint.
- A future-proof payment configuration supporting both card terminals and QR-based electronic payments in a single field.
- Removal of deprecated Community IoT/Epson paths means fewer dead settings and a cleaner upgrade baseline for 20.0.
