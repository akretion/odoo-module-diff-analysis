# im_livechat migration guide (19.0 -> 20.0)

## What's new for users
The 20.0 release notes extract provided for this addon has no im_livechat-specific content (the sections available cover features outside this module, often Enterprise-only). Everything below therefore comes from the code changes themselves.

What a Live Chat user actually sees in 20.0:
- The end-of-chat satisfaction is now a simple three-value rating: Unhappy (1), Neutral (3), Happy (5), or "Not Rated Yet", stored directly on the conversation.
- Only the customer (visitor) can rate a conversation; agents and other internal users can no longer set a rating. A system administrator can still correct one.
- Conversation tags are gone: no tag field on conversations, no tag menu, no tag column in reporting.
- Channel satisfaction reporting still relies on the last 30 days and now also shows how many ratings sit behind the percentage.

## Technical data model changes
Removed: model `im_livechat.conversation.tag` (`name`, `color`, `conversation_ids`) and the `rating.rating` override file. Fields `livechat_conversation_tag_ids` and `rating_last_text` on `discuss.channel`; `conversation_tag_ids`, `rating_id`, `rating` and `rating_text` on the member history. `discuss.channel` no longer inherits `rating.mixin`, and `im_livechat.channel` no longer inherits `rating.parent.mixin` / `_rating_satisfaction_days`.

Added:
- `discuss.channel.livechat_rating`: Selection (`1`/`3`/`5`, stored), with a SQL check constraint allowing it only on live chat conversations and only to those values.
- `discuss.channel.livechat_rating_percentage`: Float, average aggregator, computed and SQL-computed.
- `im_livechat.channel.rating_percentage_satisfaction` and `rating_count`: computed over the last 30 days.
- `im_livechat.channel.member.history.rating` (Selection, computed and stored, set for the agent/bot of the session) and `.rating_percentage` (Float, average, SQL-computed).

Changed behaviour: `discuss.channel.write()` raises a UserError when a non-visitor changes `livechat_rating` (system group excepted). `livechat_rate()` writes the selection, posts a notification with a static image, and the bus payload carries `feedback` and `rating_image_url` instead of a `rating_id`. The digest KPI now aggregates `livechat_rating` instead of `rating_get_grades`. New helpers on `discuss.channel`: `_rating_selection_to_percentage(_sql)`, `_compute_livechat_rating_percentage`, `_compute_sql_livechat_rating_percentage`; on member history: `_compute_rating`, `_compute_rating_percentage`, `_compute_sql_rating_percentage`.

## How your habits should change
- Rating: visitors keep the same experience; internal users must stop rating on behalf of customers. Free-text feedback is no longer stored as a rating field, it only appears in the conversation message.
- Tags: stop using them. Tag records and the tags already set on conversations are dropped by the upgrade. Use expertise and notes for qualification instead.
- Reporting: rebuild any report, filter or spreadsheet based on `rating.rating` or `im_livechat.conversation.tag`. Satisfaction now reads from `livechat_rating` (and `livechat_rating_percentage`), and per-agent figures from the member history fields.
- Satisfaction is now a 0-100 percentage (0 = Unhappy, 100 = Happy) usable with standard aggregators.
- Plan the data migration: both changes ship upgrade scripts (ratings, tags) — check them with your integrator before going live.

## What you gain by migrating
- A simpler model: live chat no longer drags the generic rating machinery (and its link to the chatter / `mail.thread`), so less data, less coupling and easier customisation.
- Ratings are stored on the conversation and on agent history, so per-agent and per-channel satisfaction become directly groupable, aggregatable dashboard data.
- A built-in constraint plus a permission check guarantee only customers rate, keeping your satisfaction KPIs trustworthy.
- Fewer models to maintain (no tag model), while expertise and notes still cover conversation qualification.
- A supported upgrade path with dedicated migration scripts rather than manual data fixing.
