# im_livechat migration guide (19.0 -> 20.0)

## What's new for users

The official 20.0 release notes contain nothing specific to this addon (the matched entries only describe Enterprise features that are not part of this Community deployment). The changes below come from the module's actual code diff.

- **Conversation tags are gone.** The "Live Chat Conversation Tag" model and the tag field on conversations were removed.
- **Ratings are simplified.** The generic rating machinery is replaced by one satisfaction value per conversation: Unhappy, Neutral or Happy ("Not Rated Yet" when empty). It is set by the customer and visible on the conversation and in agent reporting.

## Technical data model changes

Removed:
- Model `im_livechat.conversation.tag`, plus `livechat_conversation_tag_ids` on `discuss.channel` and `conversation_tag_ids` on `im_livechat.channel.member.history`.
- File `models/rating_rating.py` (the `rating.rating` extension for `discuss.channel`: `_compute_res_name`, `action_open_rated_object`).
- `discuss.channel` no longer inherits `rating.mixin`; `im_livechat.channel` no longer inherits `rating.parent.mixin` (`_rating_satisfaction_days`, `_rating_get_parent_field_name` removed).
- `im_livechat.channel.member.history`: `rating_id`, `rating` (float) and `rating_text` removed.

Added:
- `discuss.channel.livechat_rating`: Selection 1 Unhappy / 3 Neutral / 5 Happy, with a SQL constraint limiting it to live chat channels.
- `discuss.channel.livechat_rating_percentage`: Float, avg aggregator, computed in ORM and SQL (non-rated sessions excluded).
- `im_livechat.channel.rating_percentage_satisfaction` and `rating_count`, computed over the last 30 days.
- `im_livechat.channel.member.history.rating` (Selection, computed from the channel) and `rating_percentage` (Float with SQL aggregation).

Behavior changes:
- Writing `livechat_rating` as a non-customer now raises "Only customers can rate a live chat conversation." (system administrators excepted).
- The rating message uses a static image (`/rating/static/src/img/rating_N.png`); the agent notification carries `feedback` and `rating_image_url` instead of a rating id.
- Digest and reporting KPIs read `livechat_rating` instead of `rating.rating` records.

Method signatures: store serialization was refactored — `_to_store_defaults` / `_to_store` became `_store_*_fields(self, res)` helpers on `chatbot.script`, `chatbot.script.step`, `discuss.channel`, `discuss.channel.member`, `mail.message`, `res.partner`, `res.users.settings`, etc. Other signatures changed: `_close_livechat_session(message)`, `_get_visitor_leave_message(correspondents=...)`, `_update_forwarded_channel_data(..., operator_name)`, `digest._get_kpi_custom_settings`.

## How your habits should change

- Stop using conversation tags: clean saved filters and custom reports referencing `livechat_conversation_tag_ids` / `conversation_tag_ids`. The data is dropped at upgrade and nothing replaces it.
- Read satisfaction percentages, not star averages: the 3-step scale maps to 0 %, 50 % and 100 %, and the KPI is the average of these percentages.
- Report on `im_livechat.channel.member.history.rating`, filled only for the agent (or bot) who handled the session; the satisfaction window stays the last 30 days.
- Agents can no longer edit ratings through the generic rating object; only the customer rates (administrators can still override).

## What you gain by migrating

- A purpose-built rating decoupled from `rating.rating` / `mail.thread`, making Discuss safer and reporting faster thanks to SQL aggregation.
- Less configuration to maintain: no tag model, fewer objects to administer.
- Cleaner agent reporting: the rating is stored per agent on the session history, with satisfaction computed consistently in digests and channel KPIs.
