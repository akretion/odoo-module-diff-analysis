# im_livechat migration guide (15.0 -> 16.0)

## What's new for users

The official 15.0 → 16.0 release notes extract contains no section matching this addon, so everything below comes from the code changes shipped in `im_livechat`. The headline feature is **chatbot scripts**.

A chatbot script is a decision tree you attach to a livechat rule. When a visitor reaches a page matching that rule, the chat opens and the bot walks through the script. Available step types:

- **Text**: the bot posts a message, no answer expected.
- **Question**: the bot asks something and proposes answers the visitor clicks.
- **Email / Phone**: the bot asks for the information (email format is validated) and keeps it for later use.
- **Forward to Operator**: a human operator joins the conversation, which stops the script.
- **Free input (single line / multi-line)**: the visitor explains their issue; the text stays in the conversation.

Steps can be conditioned with an "Only If" setting on previously selected answers, so one script can contain several branches. Answers may carry a redirect link to another page. The bot's welcome messages are only written into the conversation once the visitor actually interacts, so idle chats stay clean. If "Forward to Operator" is reached and nobody is available, the script simply continues — you can build an "answering machine" that ends with "contact us by email".

A chatbot also makes the livechat button available when no human operator is online. A rule can be restricted with "Enabled only if no operator". Rule action labels were also reworded in the UI (Display the LiveChat Button / Autopop LiveChat / Hide all buttons); the underlying values are unchanged.

## Technical data model changes

New models, all inside `im_livechat`:

- **chatbot.script**: `title`, `active`, `image_1920` (taken from the bot operator partner), `script_step_ids`, `operator_partner_id` (an inactive partner automatically created and named after the script), `livechat_channel_count`, `first_step_warning`. Copying a script duplicates steps and answers and re-links the "Only If" answers to the copies.
- **chatbot.script.step**: `message`, `sequence` (auto-incremented per script on creation), `step_type` (`text`, `question_selection`, `question_email`, `question_phone`, `forward_operator`, `free_input_single`, `free_input_multi`), `answer_ids`, `triggering_answer_ids` ("Only If"), `is_forward_operator_child`.
- **chatbot.script.answer**: `name`, `sequence`, `redirect_link`, `script_step_id`, `chatbot_script_id` (related).
- **chatbot.message**: `mail_message_id` (unique per message), `mail_channel_id`, `script_step_id`, `user_script_answer_id`, `user_raw_answer`. It links a chatter message to the bot step and the visitor's raw answer; it is created automatically in `mail.channel._message_post_after_hook`.

Modified models:

- **im_livechat.channel**: new `chatbot_script_count` field and `action_view_chatbot_scripts()`; `_get_livechat_mail_channel_vals(anonymous_name, operator=None, chatbot_script=None, ...)` and `_open_livechat_mail_channel(..., chatbot_script=None, ...)` — the operator is now optional and a script's bot operator can take its place.
- **im_livechat.channel.rule**: new `chatbot_script_id` and `chatbot_only_if_no_operator`; `action` selection labels changed (values unchanged).
- **mail.channel**: new `chatbot_current_step_id`, `chatbot_message_ids`, plus helpers `_chatbot_post_message`, `_chatbot_find_customer_values_in_messages`, `_chatbot_validate_email`, `_chatbot_restart`, `_get_channel_history`.
- **mail.message**: `_message_format` now also returns `chatbot_script_step_id`, `chatbot_step_answers` and `chatbot_selected_answer_id` for livechat messages, so the frontend can show the clickable answers.

No existing field was removed or renamed. Steps that create leads or tickets come from separate bridge modules and are **not** part of `im_livechat` itself.

## How your habits should change

Nothing is imposed: 15.0 livechat rules, channels and operators keep behaving exactly as before. Chatbot scripting is strictly opt-in — you enable it by choosing a script on a livechat rule. Your main new habits are: building and maintaining scripts under the new Chatbot menu, deciding per rule whether the bot runs always or only when no operator is online, and reviewing the `chatbot.message` records (visible from the conversation) to retrieve emails/phones collected by the bot when qualifying leads.

## What you gain by migrating

- A no-code chatbot builder to qualify and orient visitors 24/7, even when no operator is online.
- Email and phone captured and reused in your CRM flows instead of being lost in the chat log.
- Conditional branching and redirect links for precise, multi-path conversations.
