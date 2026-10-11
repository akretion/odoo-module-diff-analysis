# sms migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes announce nothing for the SMS addon itself. The only SMS-related line in the extract ("WhatsApp and SMS self-order receipt") belongs to Point of Sale self-ordering, an Enterprise scope, and does not change the `sms` app of the Community edition. So there is no new "SMS feature" to advertise — but the way SMS are triggered from Server Actions has been reworked, and that is the real news of this version.

## Technical data model changes

Based on the differences between the 19.0 and 20.0 source code:

* `ir.actions.server`: the field `sms_method` (Selection: "SMS (without note)", "SMS (with note)", "Note only", label "Send SMS As") is **removed**, together with its compute method `_compute_sms_method`.
* `ir.actions.server.state`: the selection changes. The module now adds the new `log_note` state next to the existing `sms` ("Send SMS"), and no longer contributes the `followers` entry. The `ondelete={'sms': 'cascade'}` rule is unchanged.
* `sms_template_id` is unchanged (a template must belong to the action's model; it is cleared with `set null` if removed).
* `_run_action_sms_multi` no longer branches on `sms_method`. It always prepares the `sms.composer` with `default_composition_mode='comment'` and `default_mass_keep_log=True`.
* No model is added or removed; `sms.composer` and `sms.template` structures are untouched.

## How your habits should change

* In Server Actions, the "Send SMS As" dropdown disappears. Every SMS action now behaves like the former "SMS (with note)": the SMS is sent and the exchange is logged on the record.
* If you had chosen "SMS (without note)", a note is now logged anyway — accept the extra chatter trace.
* If you had chosen "Note only", use the new `log_note` action state instead: it posts an HTML note on the records and sends no SMS at all.
* Existing 19.0 SMS server actions keep running after the upgrade, but their stored "As" value is dropped and their behaviour becomes the standard send + log.

## What you gain by migrating

* Fewer, clearer choices when configuring server actions: the confusing "SMS / note" combination is gone.
* One predictable behaviour for SMS actions, with an automatic trace in the chatter — better auditability for your teams.
* A dedicated `log_note` action for pure internal notes, without abusing an SMS action.
* A simpler data model (`sms_method` removed), less custom code to maintain, and you keep your SMS workflows on the current, actively maintained version of Odoo.
