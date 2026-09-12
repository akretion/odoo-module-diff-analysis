# auth_signup migration guide (17.0 -> 18.0)

## What's new for users

The official Odoo 18.0 release notes contain no entry for `auth_signup`, so everything below comes from the code changes actually shipped in this addon. All of them are in the Community edition.

The most visible change is how invitation and password-reset links work:

* Links are no longer stored in the database. They are generated on the fly as signed, self-contained links. This is more secure: there is no token left to steal or browse.
* On the user form, the old warning banner saying a link had been sent is gone. Instead, clicking **Send Invitation Email** or **Send Password Reset Instructions** now shows a confirmation notification (toast) that the email was sent.
* Link lifetime is now always enforced and configurable through system parameters instead of being hard-coded:
  * Reset password: 4 hours by default (`auth_signup.reset_password.validity.hours`).
  * Signup / invitation: 144 hours, i.e. 6 days by default (`auth_signup.signup.validity.hours`) — invitations previously had no expiry at all.
* A link stops working as soon as the user logs in: the last login date is part of the signed payload, so a link cannot be replayed.
* Invalid and expired tokens now share one message: *"Signup token '…' is not valid or expired"*.

## Technical data model changes

`res.partner`
* Removed fields: `signup_token`, `signup_expiration`, `signup_valid`, `signup_url`. The `signup_type` field is kept.
* Removed methods: `init`, `_compute_token`, `_inverse_token`, `_compute_signup_valid`, `_compute_signup_url`, `signup_retrieve_info`.
* `signup_prepare(signup_type='signup', expiration=False)` becomes `signup_prepare(signup_type='signup')` (no expiration argument).
* Added methods: `_generate_signup_token(expiration=None)`, `_get_partner_from_token(token)`, `_get_signup_url()`, `_get_login_date()`, `_signup_retrieve_info(token)`.

`res.users`
* `_action_reset_password(signup_type='reset')`; `action_reset_password()` now returns a `display_notification` client action and chooses "signup" or "reset" from the context, so invitations and resets are clearly distinguished in logs and messages.
* `send_unregistered_user_reminder(after_days=5, batch_size=100)`: the reminder cron now follows the new batched cron guidelines.
* `authenticate(cls, db, login, password, user_agent_env)` becomes `authenticate(cls, db, credential, user_agent_env)` (introduced for passkey support; no functional impact by itself).

## How your habits should change

* After migrating, re-send invitations and password-reset emails: links generated on 17.0 are stored tokens and will no longer be recognised by 18.0.
* Do not copy a link from a "Signup URL" field anymore: that field is gone. Each email generates a fresh, single-purpose link.
* Tell your users that a reset link is now valid for only 4 hours by default; if this is too short for your organisation, raise `auth_signup.reset_password.validity.hours`.
* If your onboarding relies on invitation links staying valid for a long time, check `auth_signup.signup.validity.hours` (default 6 days) — 17.0 had no expiry for invitations.
* No database cleanup or token maintenance is required anymore, and Odoo's stored-token columns are no longer used.

## What you gain by migrating

* **Better security**: sensitive access tokens are never persisted, so a database dump or read access no longer exposes usable links.
* **Self-service validity policies**: administrators can tune invitation and reset lifetimes through system parameters, without customisation.
* **Clearer feedback**: users get an immediate notification when an invitation or reset email is sent, instead of a static hint.
* **Replay protection**: a link is automatically neutralised once the user logs in.
* **Standard, supported code**: you stay aligned with Odoo 18.0 and OCA modules (for example passkey authentication) that build on this token mechanism, and you benefit from the modernised batched cron for unregistered-user reminders.
