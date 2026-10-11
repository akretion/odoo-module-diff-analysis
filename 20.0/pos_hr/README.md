# pos_hr migration guide (19.0 -> 20.0)

This addon connects Point of Sale with HR: employees log into the POS (badge/barcode) and each one gets a role that defines what they may do at the register.

## What's new for users

No usable release-note extract was available for `pos_hr` in 20.0 (the notes we received only cover Enterprise features, which we deliberately do not present here). Everything below comes from the actual Community addon changes.

The POS employee roles have been redesigned and renamed into four levels:

- **Restrictive** (was *minimal*): can process sales and close payments, but cannot apply discounts, refunds or cancel orders.
- **Cashier** (was *basic*): full register access — sales, discounts, refunds, closing payments.
- **Supervised** (new): can process sales, but a higher level must approve before the payment is closed.
- **Manager** (was *advanced*): full access, including reporting, cash management and session close.

A new `PosAccessRightPlugin` in the POS frontend enforces these roles in the register, so the limits are no longer simple configuration labels: a restrictive employee really cannot give a discount, and a supervised employee really needs an approval.

Role lists are now mutually exclusive: putting an employee in one role automatically removes them from the other three. Employees whose user belongs to the POS Manager group can only appear in the **Manager** list.

## Technical data model changes

On `pos.config` (and mirrored on `res.config.settings`):

- removed `minimal_employee_ids`, `basic_employee_ids`, `advanced_employee_ids`;
- added `restrictive_employee_ids`, `cashier_employee_ids`, `supervised_employee_ids`, `manager_employee_ids` (settings use the `pos_*` prefix), each with its own relation table;
- strings and help texts rewritten with the new role names.

Other changes:

- `hr.employee`: the computed role now returns `restrictive`, `cashier`, `supervised` or `manager`, and `none` for employees without a role. The three per-field `@api.onchange` handlers were replaced by a shared `_pos_apply_exclusive_role()` helper.
- The employee-deletion guard now blocks deletion when the employee is used by an active session under any of the four roles (previously only the three old ones were checked).
- `PosConfig.write()` sudo handling covers the four new fields.
- `_employee_domain()` now always restricts the login employee list to people listed in one of the four roles (plus the current user's own employee). In 19.0 this restriction only applied when the *basic* list was non-empty.

## How your habits should change

- Update your vocabulary: minimal → restrictive, basic → cashier, advanced → manager.
- Review every POS configuration after migration rather than trusting the conversion. An employee you had in *basic* lands in Cashier and now does refunds and discounts; check whether Restrictive fits better.
- Assign an explicit role to everyone who must log in: leaving all four lists empty no longer means "everybody can log in".
- Use **Supervised** for trainees: they sell, and a cashier or manager approves before payment is closed.
- When adding someone to a role, check the other lists — they are removed from them automatically.
- Never leave a manager-level employee in a lower list: only the Manager list accepts POS Manager users.

## What you gain by migrating

- Finer, actually enforced permissions at the register instead of three loose labels.
- A real approval step for junior staff (Supervised), which limits discount and refund abuse.
- One consistent, self-documenting role naming across settings, POS configuration and the frontend plugin.
- A code base aligned with Odoo 20 and still maintained, instead of a version that will stop receiving fixes.
