# microsoft_outlook migration guide (15.0 -> 16.0)

## What's new for users

The official 16.0 release notes contain no section dedicated to this addon, so everything below is taken from the module's own commits for this version range.

- Outlook authentication is now a full OAuth2 flow, like Gmail. The administrator registers an Outlook application once (client ID / client secret), then each user links their own mailbox from the mail server form: a click sends you to Microsoft, you accept the permission, and you land back in Odoo with the tokens already filled in.
- It covers both outgoing SMTP servers and incoming (fetchmail) servers.
- The old "Outlook Authentication" checkbox is gone. The Authentication field of the mail server now offers "Outlook OAuth Authentication", and the form displays an explanation for each authentication method.
- The password field is hidden for Outlook (and Gmail) servers; Outlook never uses a stored password.
- Odoo validates the configuration and refuses a badly configured server early: wrong connection security, a filled password, or a from_filter that does not match the login are rejected with a clear message.

## Technical data model changes

**Removed**
- `use_microsoft_outlook_service` (Boolean, "Outlook Authentication") from `microsoft.outlook.mixin`, inherited by `ir.mail_server` and the incoming mail servers. Setups and studio fields relying on this boolean must be reworked.

**Added**
- `microsoft.outlook.mixin`, abstract model holding the OAuth tokens, the authentication URI and the token helpers.
- `ir.mail_server.smtp_authentication`: new selection value `outlook` ("Outlook OAuth Authentication"), `ondelete='set default'`.
- `res.config.settings.microsoft_outlook_client_identifier` and `..._client_secret`, mapped to the system parameters `microsoft_outlook_client_id` / `microsoft_outlook_client_secret`.
- Compute `_compute_smtp_authentication_info` (help text per method) and onchange `_on_change_smtp_user_outlook` (copies `smtp_user` into `from_filter`).

**Changed**
- `_compute_is_microsoft_outlook_configured` no longer depends on the removed boolean; `ir.mail_server` overrides it on `smtp_authentication`.
- `_compute_outlook_uri` now depends on `is_microsoft_outlook_configured` only.
- `_check_use_microsoft_outlook_service` now constrains `smtp_authentication, smtp_pass, smtp_encryption, from_filter, smtp_user`, and adds the check `from_filter == smtp_user`.
- `_onchange_use_microsoft_outlook_service` renamed to `_onchange_smtp_authentication_outlook` (triggered by `smtp_authentication`).
- `_onchange_encryption` and `_smtp_login` branch on `smtp_authentication == 'outlook'` instead of the boolean.
- Message reworded to "Please connect with your Outlook account before using it."
- Stored token fields keep their names and types, so already linked accounts keep their refresh/access tokens.

## How your habits should change

- Do not look for an "Outlook" checkbox: pick "Outlook OAuth Authentication" in the Authentication field. Host, port (587) and STARTTLS are then pre-filled correctly.
- Leave the password empty; Odoo raises an error if you set one.
- Set `from_filter` to your own address: an Outlook server can only send as the connected mailbox. Odoo fills it from the login and blocks the record otherwise.
- Have your administrator enter the Outlook client ID and secret in Settings first, otherwise the "Connect" action refuses to run.
- Linking is restricted to the Settings administrator group, and the OAuth callback is signed to prevent forged links.
- To send as other addresses, an admin must set the `mail.default.from` system parameter.

## What you gain by migrating

- Modern authentication: OAuth2 is what Microsoft currently requires and it works with MFA-enabled accounts, where plain SMTP passwords fail.
- Fewer sending failures: constraints catch a bad encryption, a stray password or a mismatched `from_filter` before the server is saved.
- Consistency: Outlook now behaves like Gmail in Odoo, and incoming mail is covered as well as outgoing.
- Safer account linking: tokens stay hidden from non-admins and the OAuth redirection is CSRF-protected.
