# mail_group migration guide (14.0 -> 15.0)

## What's new for users

`mail_group` **does not exist in 14.0**: it is a new Odoo 15.0 module. In 14.0, mailing lists were mail channels with "email sending" enabled, plus the *Website Mail Channel* module for the portal. Odoo 15.0 removes mail channels (release notes, Discuss: *"Removal of channels as followers and removal of mail channels"*) and replaces that whole flow with dedicated mail groups.

The official release notes have no other section describing this addon; the rest of this guide therefore relies on the code changes. What the new module brings:

- A dedicated mailing list app: each list is its own record with an alias, description, image, privacy (Everyone / Members only / Selected group of users) and moderators.
- A portal at `/groups` where users subscribe and unsubscribe themselves. Subscription and unsubscription links are added to the footer of every email sent by a group.
- Moderation workflow: accept, discard, reject (now with an optional subject and comment), plus **allow** (whitelist an author) and **ban** (blacklist). Allow / Ban buttons are available whatever the state of the message, so a moderator can whitelist or ban an author even when no email is pending.
- Automatic emails: guidelines sent to new subscribers, a "your message is waiting moderation" notice to authors, and a cron notifying moderators of pending messages.
- A reworked backend form, portal template and mobile display; attachments are shown under every message; rejected messages are hidden, even for administrators.
- Bug fixes: a double click on a confirmation link no longer raises an error; the website snippet no longer shows "Unsubscribe" after switching to a group you are not a member of; month filtering of portal messages is now correct.

## Technical data model changes

New models added by the module: `mail.group` (description changed from *Mailing List* to *Mail Group*), `mail.group.member`, `mail.group.message` and `mail.group.moderation`.

On `mail.group`:
- Removed `can_manage_members`; replaced by `can_manage_group` (admin, superuser or moderator).
- Added `active`, `alias_fullname` (computed), `is_member` (current user), `mail_group_message_count`.
- `moderation_notify_msg` and `moderation_guidelines_msg` changed from **Text to Html**.
- `name` is now translatable; default order is `create_date DESC, id DESC`; the default group image is gone.

On `mail.group.message`:
- The inverse of `group_message_parent_id` was removed.
- Added `author_moderation` (Banned / Whitelisted) and `is_group_moderated`.

Methods and behaviour:
- `action_allow` → `action_moderate_allow`, `action_ban` → `action_moderate_ban`; new `action_moderate_ban_with_comment`.
- New `action_join` / `action_leave`; `_leave_group(email, partner_id, all_members=False)`.
- `_compute_can_manage_members` → `_compute_can_manage_group`; new `_compute_alias_fullname`, `_compute_is_member`, `_clean_email_body`.
- Guidelines are sent through `mail_template_guidelines` (formerly `mail_group_send_guidelines`) using `send_mail()`.
- ACLs tightened: only administrators, the responsible or moderators may write / delete a group; the responsible of a non-moderated group can now read the members.
- Emails are cleaned on arrival: the mailing list footer is stripped, Outlook reply quoting is handled, and an `In-Reply-To` header is set.
- Duplicate email addresses (several contacts sharing one email) are handled: only one email is sent per address, the most relevant member is returned, and unsubscribing removes all members with that address.
- Routes changed: `/groups/subscription` is split into `/group/subscribe` and `/group/unsubscribe`; confirmation links become `/group/<action>-confirm`.

## How your habits should change

- Channel-based mailing lists disappear: recreate each list as a `mail.group` with its members (an upgrade script performs the conversion — check it runs on your database).
- Use the `/groups` portal for subscriptions; old `/groups/subscription` links and already-sent footer links will no longer work.
- Moderators should review their buttons: *Accept / Reject / Discard* remain, *Allow* and *Ban* are permanent rules on the author, and can be applied at any time.
- Guidelines and notification texts are now rich HTML, so existing plain-text content should be reviewed in the new editor.
- Replies now thread correctly, and the group footer is no longer duplicated in long email threads.

## What you gain by migrating

- A maintained, dedicated mailing list app built for Odoo 15 rather than a repurposed chat channel.
- Stronger moderation: whitelists, blacklists, reject-with-comment, always-available allow / ban.
- A self-service portal plus mobile-friendly reading and attachments visible in place.
