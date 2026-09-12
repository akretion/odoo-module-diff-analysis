# purchase_requisition migration guide (15.0 -> 16.0)

## What's new for users

**Call for Tenders, revamped.** The official 16.0 release notes state: *"Create alternative requests for quotations and compare them easily, globally or line per line. Creating a dedicated purchase agreement is not needed anymore."* In practice, from an RFQ you can now:

- Use **Create Alternative** to spin up another RFQ for a different vendor. A wizard asks whether to copy the products and quantities of the original RFQ.
- See all linked RFQs in an **Alternative POs** field and switch between them without leaving the form (all alternatives stay interconnected).
- Use **Compare Order Lines** to show the lines of all linked RFQs side by side, with best total price, best unit price and best planned date highlighted. Buttons let you *choose* a line, or *clear* the competing quantities (set to 0) so only the winning RFQ keeps quantity.
- Get a warning when confirming an RFQ that still has open alternatives, so you can decide what happens to them. POs already confirmed or done are never cancelled by that cleanup.

**Analytics on requisition lines.** Requisition lines now use the standard 16.0 analytic distribution widget (analytic plans and distribution models). Analytic tags no longer exist, and the single "Analytic Account" field is replaced by that distribution.

This addon ships in the Community edition and the features above are part of it. The other Purchase release-notes items (bill price matching, purchase history, receipt status) belong to the `purchase` app, not to `purchase_requisition`.

## Technical data model changes

`purchase.requisition.line`
- Now inherits `analytic.mixin`: gains `analytic_distribution`.
- Removed: `account_analytic_id`, `analytic_tag_ids`.
- Requisition-to-PO line generation now copies `analytic_distribution` instead of the two removed fields.

`purchase.order`
- Added: `purchase_group_id` (m2o `purchase.order.group`), `alternative_po_ids` (o2m, writable, restricted to draft/sent/to-approve POs of the same company), `has_alternatives` (computed boolean).
- `button_confirm()` can now return a warning action when open alternatives exist (bypassed by the `skip_alternative_check` context key).
- New methods: `action_create_alternative()`, `action_compare_alternative_lines()`, `get_tender_best_lines()`.
- `create()` / `write()` extended to build and maintain alternative-PO groups.

New model `purchase.order.group` (technical): holds `order_ids` and deletes itself as soon as fewer than two POs remain linked.

New wizards and views: `purchase.requisition.create.alternative`, `purchase.requisition.alternative.warning`, and a comparison list view for `purchase.order.line`.

`purchase.order.line`: new `action_clear_quantities()` and `action_choose()` methods.

No method signature changes, and `purchase.requisition` itself is untouched.

## How your habits should change

- Stop thinking "I need a purchase agreement to compare quotes". Alternatives are linked directly between RFQs. The historical flow (`requisition_id` on the PO) still works and is unchanged.
- Analytic accounting moves to distribution: rebuild your mappings as plans and distribution models. Previous tag values are not carried over, so plan a data check during migration.
- Linking alternatives is bi-directional and transitive; you cannot link an unsaved PO, and the form auto-saves when you click another alternative PO.
- "Clear quantities" only affects draft/sent/to-approve POs; you get a notification for lines it skipped.
- Comparison and "choose a line" now happen on a dedicated list view rather than by reading each RFQ manually.

## What you gain by migrating

- A much faster tender loop: no intermediate agreement record, fewer clicks, everything visible on the RFQ.
- Built-in comparison with visual best-price and best-date hints instead of opening each RFQ one by one.
- Analytics aligned with the Accounting app, ready for 16.0 analytic plans and reports.
- A supported platform for your procurement flows, with the new alternative-PO workflow in the Community edition.
