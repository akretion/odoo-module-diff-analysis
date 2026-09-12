# mail migration guide (14.0 -> 15.0)

## What's new for users

In 15.0 mail channels are purely chat-oriented. The "mailing list" behaviour of a channel — sending its messages by email and moderating them — was removed from channels and re-implemented in a separate module. A channel is now a chat, a group chat or a private discussion.

- Invite people as members of a channel; the member list is visible in Discuss.
- Simplified channel notifications and a reworked sidebar: channels, chats and group DMs are easier to find, with avatars in the DM section.
- Start a chat from the command palette (Ctrl+K); create a group from a direct message.
- Edit or delete a message, and react to it with a smiley.
- Channels are no longer followers: only partners (people) can follow a record.
- Email templates are QWeb-based and edited in their final rendering, so you customise them without touching code.
- A new Settings option restricts template writing to members of the "Mail Template Editor" group.
- Activity types: clearer configuration, and non-administrators can access the Activity Types menu again.

## Technical data model changes

Removed model: mail.moderation.

mail.channel — removed: moderation, moderator_ids, is_moderator, moderation_ids, moderation_count, moderation_notify, moderation_notify_msg, moderation_guidelines, moderation_guidelines_msg, email_send, channel_message_ids.

mail.message — removed: moderation_status, moderator_id, need_moderation, channel_ids. A message now belongs to exactly one thread (model + res_id); the "listener channel" mechanism is gone and the related access-rule exceptions were simplified.

mail.followers — removed: channel_id; partner_id is now required; name/email/is_active are related fields on partner_id. The channel uniqueness / XOR SQL constraints were dropped, and internal helpers (_insert_followers, _add_followers, _get_recipient_data, _get_subscription_data) no longer take channel arguments.

mail.activity.type — force_next replaced by chaining_type (suggest / trigger); default_next_type_id → triggered_next_type_id; next_type_ids → suggested_next_type_ids; default_description → default_note; res_model_id (many2one ir.model) → res_model (selection computed in sudo, so no ir.model read access is needed); initial_res_model_id → initial_res_model. Labels changed too: "Scheduled Date" → "Schedule", "after validation date" → "after completion date", "Action to Perform" → "Action". On mail.activity, force_next is replaced by the related chaining_type.

mail.composer.mixin — new is_mail_template_editor field; can_edit_body is False when a template is selected and the user is not a template editor; _render_field adapted.

mail.render.mixin — new _unrestricted_rendering attribute; rendering a dynamic template without the template-editor group raises AccessError, and static content is returned as-is.

New group mail.group_mail_template_editor and parameter mail.restrict.template.rendering (IrConfigParameter.set_param toggles it on base.group_user). Deleting an ir.model now also deletes its activity types.

## How your habits should change

- Stop using channels as mailing lists. Moderation queues, "Check messages" in Discuss, guidelines emails, the channel subject and email sending are gone; mailing-list features now live in a separate module.
- You can no longer follow a document with a channel — add the people instead. Conversely, a message is displayed in a single chatter.
- Configure activity types with "Chaining Type": Trigger (the next activity is scheduled automatically when you complete the current one) or Suggest (the next activity is proposed). The old "Trigger Next Activity" checkbox is gone.
- If template restriction is enabled, request the Mail Template Editor group before editing dynamic templates; otherwise the body is read-only once a template is selected.

## What you gain by migrating

- A leaner data model: channels are chats, followers are people — fewer surprises in notifications and access rights.
- Mailing-list features are cleanly separated instead of being squeezed into the channel model.
- Fine-grained control over who writes email templates, with safer rendering of dynamic Jinja content.
- Better activities: clearer chaining, a default note, and Activity Types accessible to all users, not only administrators.
- A modernised Discuss (members, sidebar, editing/reactions) and QWeb email templates edited in their final rendering.
