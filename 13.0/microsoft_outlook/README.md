# microsoft_outlook migration guide (12.0 -> 13.0)

## What's new for users

The `microsoft_outlook` addon **does not exist in Odoo 12.0**: it appears between 12.0 and 13.0 as a brand-new module. There is no legacy behaviour to preserve — everything below is an addition.

It brings OAuth 2.0 authentication to **outgoing Outlook mail servers** (`ir.mail_server`), the same way Gmail was handled. Concretely:

- An administrator registers Odoo as an application at Microsoft and stores the **Outlook Client Id** and **Client Secret** in Settings (system parameters `microsoft_outlook_client_id` / `microsoft_outlook_client_secret`).
- On a mail server form, the admin ticks **Outlook Authentication**. Odoo then pre-fills the server: `smtp.outlook.com`, port 587, "TLS (STARTTLS)".
- An action opens Microsoft's consent page. Once the user accepts, the browser is redirected back to `/microsoft_outlook/confirm` and the tokens are stored on the mail server record automatically — no token copy/paste, no password.
- Odoo enforces the rules: the password field must stay empty and connection security must be STARTTLS, otherwise an explicit error is shown.

The incoming-mail side of the same feature ships in the companion `fetchmail_outlook` module, which is not part of this patch.

No section of the official 13.0 release notes covers this addon; the description above comes from the module diff only.

## Technical data model changes

All changes are additive — the diff reports **no method signature change** on existing models.

New abstract model `microsoft.outlook.mixin`:

| Field | Type | Notes |
|---|---|---|
| `use_microsoft_outlook_service` | Boolean | "Outlook Authentication" |
| `is_microsoft_outlook_configured` | Boolean, computed | true when client id + secret are set |
| `microsoft_outlook_refresh_token` | Char | system admins only, `copy=False` |
| `microsoft_outlook_access_token` | Char | system admins only, `copy=False` |
| `microsoft_outlook_access_token_expiration` | Integer (timestamp) | system admins only, `copy=False` |
| `microsoft_outlook_uri` | Char, computed | consent URL, system admins only |

Methods added on the mixin: `open_microsoft_outlook_uri`, `_fetch_outlook_refresh_token`, `_fetch_outlook_access_token`, `_fetch_outlook_token`, `_generate_outlook_oauth2_string`, `_get_outlook_csrf_token`.

- `ir.mail.server` now inherits `microsoft.outlook.mixin` and adds the constraint `_check_use_microsoft_outlook_service`, an onchange on `use_microsoft_outlook_service`, an override of `_onchange_encryption` (left untouched for Outlook servers so host/port stay as set) and an override of `_smtp_login`, which switches to `AUTH XOAUTH2` when a single Outlook server record is used.
- `res.config.settings` gains `microsoft_outlook_client_identifier` and `microsoft_outlook_client_secret`.
- Access tokens last about 1 hour (expiry returned by Microsoft) and are refreshed transparently from the refresh token before each use.

## How your habits should change

- **Stop using passwords for Outlook.** Leave the password empty; any value triggers "Please leave the password field empty for Outlook mail server…".
- **Keep STARTTLS.** Choosing another connection security raises "Incorrect Connection Security for Outlook mail server".
- **Consent is a one-time manual step** performed by a system administrator; only that group sees the token fields and can start the authorization.
- **Re-authorization may be required** if the consent is revoked or a refresh token becomes invalid — this is done per mail server record, not through a global setting.
- Password-based authentication for other providers is unchanged.

## What you gain by migrating

- **Future-proof authentication**: Microsoft is retiring basic SMTP AUTH, so OAuth keeps outgoing mail flowing on Microsoft 365, including with MFA.
- **No mail password stored in Odoo**, and tokens restricted to system administrators.
- **Fewer outages**: token refresh is automatic, so no more expired app passwords to rotate.
- **Safer flow**: a CSRF-signed state prevents a malicious link from disconnecting your mail servers.
- **Low migration cost**: the module is new, so no data migration of existing settings is needed — only the Outlook credential configuration.
