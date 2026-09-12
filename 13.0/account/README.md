# account migration guide (12.0 -> 13.0)

## What's new for users

- **Invoices and journal entries are one object.** Customer invoices, vendor bills and journal entries now share a single record (the journal entry). A draft invoice simply is a draft entry; validating it posts the entry.
- **Taxes are easier to configure.** Each tax now defines how its amount is split ("repartition") between base and tax lines and where each part is posted, separately for invoices and credit notes.
- **Better reconciliation:** manual reconciliation for a partner shows all open entries, several taxes can be added on a write-off, reconciled entries are easier to find (search, stat buttons) and bank suspense entries are matched more easily.
- **Payments:** registering payments from a list creates one payment per invoice; split payments for partial allocation; cleaner payment form.
- **Communication reference per journal** (free / based on customer / based on invoice, in Odoo or European RF format).
- **Closing:** lock dates to close periods, plus a tax lock date keeping you from posting in a closed tax period.
- **New Off-Balance sheet account type** and an IBAN check widget on bank accounts.
- Everything above is Community. Assets, Accrual/Deferral, documents/OCR and SMS follow-up are Enterprise and are not part of this addon.

## Technical data model changes

- **Removed models:** account.invoice, account.invoice.line, account.invoice.tax, account.invoice.confirm, account.invoice.refund, account.voucher, account.voucher.line (the account_voucher module is gone). Use account.move / account.move.line.
- **Field renames (invoice → move):** number→name, reference→ref, comment→narration, user_id→invoice_user_id, residual→amount_residual, date_invoice→invoice_date, date_due→invoice_date_due, sent→invoice_sent, origin→invoice_origin, payment_term_id→invoice_payment_term_id, incoterm_id→invoice_incoterm_id. On moves: amount→amount_total, reverse_entry_id→reversed_entry_id.
- account.move.line also holds invoice lines and tax lines: display_type, exclude_from_invoice_tab, tax_line_id, tax_repartition_line_id, tag_ids, tax_audit, price_subtotal, quantity, product_uom_id, tax_ids.
- **New models:** account.tax.repartition.line, account.tax.report.line. account.account.tag gains tax_negate/country_id and is linked from move lines.
- account.tax: account_id / refund_account_id / tag_ids replaced by invoice_repartition_line_ids and refund_repartition_line_ids; cash_basis_account_id → cash_basis_transition_account_id (cash-basis tax stays on the transition account until reconciliation).
- account.payment: account.abstract.payment and account.register.payments removed; new 'transfer' type, invoice_ids, move_line_ids, reconciled_invoices_count.
- account.journal: group_invoice_lines removed; post_at_bank_rec → post_at ('pay_val' / 'bank_rec'); invoice_reference_type and invoice_reference_model moved from the company to the journal.
- account.reconcile.model: tax_id / second_tax_id (m2o) → tax_ids / second_tax_ids (m2m).
- account.move: auto_reverse and reverse_date replaced by auto_post, posted by a cron; amount becomes invertible.
- account.move.line: cash-basis report fields (debit/credit/balance_cash_basis) removed.
- account.fiscal.position: zip_from / zip_to are now Char (was Integer).
- Method renames: _run_post_draft_to_post, action_invoice_register_payment, action_open_matching_suspense_moves; invoice refunds are now done with account.move.reversal.

## How your habits should change

- Encode an invoice through the journal entry form: you edit the accounting data directly and the invoice view reflects it.
- Lines are no longer grouped by product at validation, and the journal's "Group Invoice Lines" option is gone.
- Draft invoices now appear in reports and budgets.
- Payments: one payment per invoice, with the amount pre-filled with the residual and no longer editable — use the payment difference (write-off / keep open) instead.
- Labels changed: "Unposted" is "Draft", "Posted" is "Validated" (payments), "Payment Status" is "Payment".
- Vendor-bill file import was removed from account; use a dedicated module (e.g. l10n_be_edi for UBL XML).

## What you gain by migrating

- One consistent record to edit: correcting a journal item updates the related invoice, avoiding double encoding.
- Reliable tax reports: amounts are tagged directly on journal items, so a later tax configuration change no longer rewrites past reports.
- Simpler payments, stronger reconciliation and closing tools (lock dates, suspense matching).
- You stay on a supported version and benefit from the 13.0 usability improvements.
