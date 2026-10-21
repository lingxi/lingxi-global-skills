# Form settings fields: 8 sections

For general conventions on paths, types, updates and uploads, see [form-definition.md](form-definition.md). The other two sections are in [payment settings](form-payment-fields.md) and [theme settings](form-theme-fields.md). All `Y/N` values use the integers 1/2.

## Shared nested structures

### User references

Applicable field prefixes: `definition.config.common_config.permissions.manage.users[]`, `definition.config.common_config.permissions.edit.users[]`, `definition.config.common_config.permissions.view.users[]`, `definition.config.automation_config.audit.audit_type.types[].users[]`. Notification `recipients.users[]` also uses this structure.

A user element can be a scalar ID that the current tool supports, or the object below; never guess the ID encoding. Use `id=0` only on permission nodes that explicitly support "all users"; it cannot stand for an arbitrary admin.

| Field | Type | Purpose |
| --- | --- | --- |
| `id` | integer | Real organization user identifier; resource ownership and selectable scope are also validated by the server. |
| `username` | string | Display name; do not use the name in place of the ID. |
| `is_creator` | scalar flag | Creator flag in the server's default recipient/permission candidates; do not use it to transfer the creator. |

### Notification nodes

Field prefixes: `definition.config.notification_config.admin_notification`, `definition.config.notification_config.user_notification`, `definition.config.automation_config.audit.notification.admin_notification`, `definition.config.automation_config.audit.notification.user_notification`, `definition.config.payment_config.receipt.user_notification`

| Field | Type/limit | Purpose |
| --- | --- | --- |
| `enabled` | Y/N | Whether this node's messages are triggered. |
| `notifications` | list, max 100 | Template config per business scenario and channel. |
| `notifications[].scenario_code` | string | Business scenario code, such as submit success, under approval, approval rejected, receipt; follow the live templates and never compose guessed values. |
| `notifications[].channel` | string | Channel; the current example is `email`; a value the Schema allows does not mean SMS or other channels can send. |
| `notifications[].action` | string/scalar | Business action identifier, a compatibility field for legacy templates; keep it consistent with the scenario and use the structure the tool returns. |
| `notifications[].recipients` | object | Recipient config for admin messages; recipients of user messages are resolved from the business context. |
| `notifications[].recipients.users` | list, max 500 | Organization user references, scalar ID or object. |
| `notifications[].recipients.users[].id` | integer | Recipient user identifier. |
| `notifications[].recipients.users[].username` | string | Recipient user display name. |
| `notifications[].recipients.users[].is_creator` | scalar flag | Creator default-recipient flag. |
| `notifications[].subject` | string/null | Email subject source text; preserve template variables. |
| `notifications[].body` | string/null | Message body source text; preserve template variables and rich text. |

Templates are synced to business storage such as `notify_template`; saving this JSON alone does not prove that sending succeeds. Still check the organization's notification capability, the corresponding scenario, the contact questions and template completeness. The edit endpoint may backfill the creator as a recipient; the save endpoint backfills only existing admin templates and never creates templates from the enabled switch.

### Config image references

Field prefixes: `definition.config.ext_config.weixin.share.image`, `definition.config.share_config.portal_publish.images.mobile`, `definition.config.share_config.portal_publish.images.pc`, `definition.config.share_config.social_media.media.*.image`

| Field | Type | Purpose |
| --- | --- | --- |
| `upload_id` | UUID | Trusted reference to a new upload; some image nodes accept it through an open structure, and the Request validates it separately. |
| `id` | current file reference | Existing file identifier; saving also validates ownership. |
| `path` | string, read back | File path display value. |
| `url` | string, read back | Accessible image URL; not an entry point for importing arbitrary external links. |
| `original_name` | string, read back | Original file name. |

## common_config: participation, permissions and agreements

Field prefix: `definition.config.common_config`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `policies` | object | Participation policy container. |
| `policies.participation` | object | Participation conditions for respondents. |
| `policies.participation.login_required` | Y/N | Whether login is required; separate from the portal participation login setting. |
| `policies.participation.claim_required` | Y/N | Claim requirement declared by the Schema; actual enforcement was not located in the form domain or components in this review, so do not promise it blocks anyone. |
| `policies.participation.age` | object | Age condition container; only the declaration has been located so far. |
| `policies.participation.age.min` | integer | Declared minimum age; do not promise it takes effect on this basis. |
| `policies.participation.age.max` | integer | Declared maximum age; do not promise it takes effect on this basis. |
| `policies.participation.genders` | list, max 20 | Declared allowed gender codes; enforcement not located; use real candidates and do not infer from examples that the restriction is active. |
| `permissions` | object | Internal organization permissions for the form. |
| `permissions.manage` | object | Management abilities such as editing questions/settings/sharing, maintaining permissions, and deleting. |
| `permissions.manage.users` | user reference list, max 500 | Designated managers; for default creator handling, see the default service. |
| `permissions.edit` | object | Editing abilities such as submission approval, editing registrations and confirming payments; does not mean the form definition can be changed. |
| `permissions.edit.users` | user reference list, max 500 | Designated submission editors. |
| `permissions.view` | object | Read-only access to form settings and submitted information. |
| `permissions.view.users` | user reference list, max 500 | Designated viewers; the supported "all users" candidate is provided by the server. |
| `privacy_policy` | object | User agreement/privacy statement. |
| `privacy_policy.name` | string | Public display name; max 15 characters if it contains Chinese characters, max 60 characters if pure English; blank is normalized to the default name. |
| `privacy_policy.confirm_required` | Y/N | Whether the respondent must first confirm having read it before entering and submitting. |
| `terms_of_service` | object | Terms of service; lacks the name/confirm_required extensions of privacy_policy. |

Field prefixes: `definition.config.common_config.privacy_policy`, `definition.config.common_config.terms_of_service`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `enabled` | Y/N | Whether to show/use this agreement. |
| `source` | `team` / `custom` | Organization default agreement or form-specific custom agreement. |
| `language` | language code | Top-level compatibility language field; for multilingual body text, the agreements entries are authoritative. |
| `content` | string/null | Top-level compatibility body; use it as the live template does and do not create conflicts with the entries. |
| `agreements` | list, max 50 | Agreement content saved per language. |
| `agreements[].language` | currently supported language code | Entry language; required and validated by the Request. |
| `agreements[].content` | string/null | Agreement body in this language. |
| `agreements[].upload_id` | UUID/null | Trusted new-upload reference for the agreement attachment. |
| `agreements[].original` | Y/N, echo flag | 1 means the organization's original agreement, 2 means a form-specific custom version; the frontend uses it to tell the source apart; it is not a foreign key for the body or attachment. |

Never send back `file_id/file/file_path` as trusted references for agreement attachments. Custom body text enters the revision snapshot; the relationship between the default agreement and the organization's policies is handled by the agreement service.

## submit_config: submission limits

Field prefix: `definition.config.submit_config`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `time_range_close_rule` | object | Time range during which the form is open for submissions. |
| `time_range_close_rule.enabled` | Y/N | Whether to limit the open period. |
| `time_range_close_rule.start_time` | datetime string | Submission start time; not the activity start date. |
| `time_range_close_rule.end_time` | datetime string | Submission deadline; cannot be earlier than the start time. |
| `time_range_close_rule.not_open_message` | string | Message shown before the open time. |
| `time_range_close_rule.already_closed_message` | string | Message shown after the deadline. |
| `submission_quota` | object | Total submission quota limit. |
| `submission_quota.enabled` | Y/N | Whether to enable the quota limit. |
| `submission_quota.quota` | positive integer | Must be >0 when enabled, and changes must pass the consumed-quota validation; 0 cannot mean unlimited. |
| `submission_quota.message` | string | Message shown when the quota is full. |
| `allow_anonymous` | scalar | Anonymous submission declared by the Schema; no corresponding control or enforcement was located in this review, so do not promise that users are not recorded. |
| `frequency` | object | Submission frequency config; currently the duplicate-submission switch is explicitly supported. |
| `frequency.not_allow_multiple_submissions` | object | Rule that disallows duplicate submissions. |
| `frequency.not_allow_multiple_submissions.enabled` | Y/N | 1 forbids duplicates, 2 allows duplicates. |
| `frequency.not_allow_multiple_submissions.condition` | string list, max 50 | Duplicate-check dimensions: `username/mobile/email/team_openid/ip`. Contact dimensions use type_code and system dimensions use item_key; not arbitrary question IDs. |
| `password_required` | object | Require a password to open the form. |
| `password_required.enabled` | Y/N | Whether to verify the password. |
| `password_required.password` | string | Access password; when enabled, it must meet the minimum length in the organization dictionary. |
| `password_required.min_length` | number, candidate | Minimum-length hint in the edit UI; the real limit is set by the server dictionary, and submitting a smaller value cannot relax it. |
| `auto_fill_submitter` | object | Setting that auto-fills contact information from earlier submissions. |
| `auto_fill_submitter.enabled` | Y/N | Whether it is enabled; identity/device context and client capability still affect auto-fill. |
| `sequence_number` | object | Automatic numbering in front of questions; not the submission special code. |
| `sequence_number.enabled` | Y/N | Whether to show question numbers. |
| `sequence_number.selected` | `A/a/1/I` | Uppercase letter, lowercase letter, number or Roman numeral format. |
| `sequence_number.format` | string list, max 50, candidates | Available numbering formats; does not mean several formats are enabled at once. |
| `blacklist_and_whitelist` | object | Restricts who can submit based on organization contact scope. |
| `blacklist_and_whitelist.enabled` | Y/N | Whether it is enabled; also subject to organization capability. |
| `blacklist_and_whitelist.black_or_white` | `black` / `white` | Exclude listed contacts, or allow only listed contacts. |
| `blacklist_and_whitelist.condition` | string list, max 100 | Match dimensions: `mobile/email`; the form must have the corresponding question, and it cannot be a jump target. |
| `blacklist_and_whitelist.categories` | object list, max 500 | Contact categories used as list sources. |
| `blacklist_and_whitelist.categories[].id` | real ID | Contact category identifier in the current organization. |
| `blacklist_and_whitelist.categories[].title` | string, candidate | Category display name. |
| `blacklist_and_whitelist.tags` | object list, max 500 | Contact tags used as list sources. |
| `blacklist_and_whitelist.tags[].id` | real ID | Tag identifier in the current organization. |
| `blacklist_and_whitelist.tags[].title` | string, candidate | Tag display name. |
| `blacklist_and_whitelist.contacts` | object list, max 500 | Explicitly selected contacts. |
| `blacklist_and_whitelist.contacts[].id` | real ID | Contact identifier in the current organization. |
| `blacklist_and_whitelist.contacts[].name` | string, candidate | Contact display name. |
| `blacklist_and_whitelist.message` | string | Message shown when the respondent is outside the allowed scope. |
| `enrollment_type` | object | Self-only or multi-person registration setting. |
| `enrollment_type.default` | `self` / `multiple` | Default candidate value. |
| `enrollment_type.selected` | `self` / `multiple` | Mode currently enabled: self only, or registering for others / several people at once. |
| `enrollment_type.options` | object list, max 50, candidates | Available registration modes. |
| `enrollment_type.options[].code` | string, candidate | Registration mode code. |
| `enrollment_type.options[].title` | string, candidate | Registration mode name. |

The three blacklist/whitelist lists and enrollment_type.options are open elements; this manual lists the id/title/name/code used by the Schema example and actually used by the controls. Other keys have no general enforcement semantics. Contact questions used for duplicate checks must be required, and before publishing there must also be a question that is not a jump target to carry that dimension. Saving an activity syncs the registration period, quota, duplicate registration and registration mode into this section, so do not submit two conflicting configs at the same time.

## submitted_config: data handling after submission

Field prefix: `definition.config.submitted_config`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `contact_collect_mode` | object | Global policy for collecting into contacts. |
| `contact_collect_mode.enabled` | Y/N | Whether to collect into contacts; also check the question-level collect_to_contact. |
| `contact_collect_mode.selected` | `ignore` / `replace` | Keep existing data, or replace it and record the change; use the strategy the user confirmed and do not copy replace from the example. |
| `contact_collect_mode.default` | `ignore`, candidate | Product default candidate; actual handling follows selected. |
| `contact_collect_mode.collect_mode` | object list, max 50, candidates | List of collection modes. |
| `contact_collect_mode.collect_mode[].code` | string | `ignore/replace` code. |
| `contact_collect_mode.collect_mode[].title` | string | Collection mode name. |
| `special_code` | object | Container for submission special code settings. |
| `special_code.auto_generate_special_code` | object | Generates a code on successful submission and writes it into the submission. |
| `special_code.auto_generate_special_code.enabled` | Y/N | Whether to generate the special code. |
| `special_code.auto_generate_special_code.collect_to_contact` | object | Syncs the code to a contact attribute. |
| `special_code.auto_generate_special_code.collect_to_contact.enabled` | Y/N | Whether to sync the code. |
| `special_code.auto_generate_special_code.collect_to_contact.anchor` | string | Real contact field anchor in the current organization. |
| `special_code.auto_generate_special_code.collect_to_contact.can_change_anchor` | Y/N, candidate | Whether the designer may change the anchor. |
| `special_code.auto_generate_special_code.generate_rule` | object | Rule for composing the code. |
| `special_code.auto_generate_special_code.generate_rule.default` | `system`, candidate | Default rule type. |
| `special_code.auto_generate_special_code.generate_rule.selected` | `system` / `custom` | Use the system or custom segments. |
| `special_code.auto_generate_special_code.generate_rule.system` | segment list, max 100 | System default rule; keep the currently returned value. |
| `special_code.auto_generate_special_code.generate_rule.custom` | segment list, max 100 | Custom code, concatenated in list order. |
| `category_and_tag` | object | Category/tag rules applied after collecting into contacts. |
| `category_and_tag.enabled` | Y/N | Linked with global collection; not a fully independent switch. |
| `category_and_tag.user_behavior` | list, max 100 | Adds categories/tags based on user behavior. |
| `category_and_tag.user_behavior[].action` | scenario code | Current behavior, such as submit success; use business candidates and do not guess new actions. |
| `category_and_tag.user_behavior[].category` | ID list, max 100 | Contact categories to add to; legacy single values are accepted and normalized to a list on save. |
| `category_and_tag.user_behavior[].tag` | ID list, max 100 | Tags to add; legacy single values are accepted and normalized to a list on save. |
| `category_and_tag.item_types` | list, max 500 | Adds categories/tags based on the options selected in a question. |
| `category_and_tag.item_types[].item_key` | string | Stable key of the source question. |
| `category_and_tag.item_types[].option_value` | scalar or list, max 500 | Option value that triggers the rule; not the option label. |
| `category_and_tag.item_types[].code` | question type code | Auxiliary identifier of the source question type; match the real question and do not copy the wrong question type from the example. |
| `category_and_tag.item_types[].category` | ID list, max 100 | Contact categories to add to on a match. |
| `category_and_tag.item_types[].tag` | ID list, max 100 | Tags to add on a match. |

Field prefixes: `definition.config.submitted_config.special_code.auto_generate_special_code.generate_rule.system[]`, `definition.config.submitted_config.special_code.auto_generate_special_code.generate_rule.custom[]`

| Field | Type/values | Purpose |
| --- | --- | --- |
| `format` | `character/symbol/date/auto_increment/random_number` | Segment of fixed letters/text, symbol, date, sequence number or random digits. |
| `value` | string/number | Content of this segment; dates use format codes the system supports. |
| `in` | scalar list, max 100, candidates | Available values for symbol or date formats, such as `yymmdd/yyyymmdd/yymm/mmdd`; they are not all concatenated. |
| `start` | number | Start value of the auto-increment sequence. |
| `length` | number | Number of digits for auto-increment/random digits; validated by the code business rules. |

Enabling contact collection requires a name question with collection enabled; disabling global collection turns off question-level collection and the category/tag linkage. Custom contact field anchors and IDs must belong to the current organization. The service that actually generates codes is not the same capability as `automation_config.auto_generate`.

## notification_config: messages and the submit success page

Field prefix: `definition.config.notification_config`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `translation_enabled` | Y/N | Form-level master switch for email translation; all enabled email nodes enter the translation catalog. |
| `admin_notification` | shared notification object | Business messages to admins. |
| `user_notification` | shared notification object | Business messages to respondents. |
| `customized_feedback` | object | Custom success page content. |
| `customized_feedback.enabled` | Y/N | Whether to show custom feedback. |
| `customized_feedback.body` | string/null | Success page body, supports product rich text; a successful submission does not necessarily mean approved/paid. |
| `submission_view_qrcode` | object, replaced as a whole | QR code credential for viewing the submission. When changing it, first read the original config, then submit the complete object with the 4 fields below, keeping unchanged settings; passing only some fields loses the other settings. `expires_at` is not accepted. |
| `submission_view_qrcode.enabled` | Y/N | Whether the success page offers a QR code for viewing this submission; missing means off. |
| `submission_view_qrcode.title` | string/null, max 200 characters | Plain-text title, supports `{{form_title}}`; null uses the default title in the current language. |
| `submission_view_qrcode.body` | string/null, max 1000 characters | Plain-text description; null uses the default description, and an empty string shows nothing. |
| `submission_view_qrcode.validity_days` | integer 1..3650 | **Required when enabled**, edit default 7. Expiry is counted from the submission creation time; scanning or obtaining the credential again does not extend it. |
| `promotion_link` | object | Promotion entry at the bottom of the success page. |
| `promotion_link.enabled` | Y/N | Whether to show the promotion entry. |
| `promotion_link.button_text` | string | Button text. |
| `promotion_link.selected` | `href` / `vendor` | External link or third-party activity tool mode. |
| `promotion_link.actions` | list, max 20 | Config for each promotion mode. |
| `promotion_link.actions[].type` | string | There must be an entry matching selected. |
| `promotion_link.actions[].link` | valid URL | When enabled, the selected action must have a valid link. |

## automation_config: submission approval

Field prefix: `definition.config.automation_config`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `audit` | object | Registration/submission approval settings; separate from the publish approval workflow. |
| `audit.enabled` | Y/N | Whether submissions need approval; current business rules do not allow payment tier questions at the same time. |
| `audit.audit_type` | object | Approver mode. |
| `audit.audit_type.selected` | `admin` / `assign` | Approval by managers or by designated people. |
| `audit.audit_type.types` | list, max 20 | Approver settings and candidates for each mode. |
| `audit.audit_type.types[].code` | string | Mode code that matches selected. |
| `audit.audit_type.types[].title` | string, candidate | Mode name. |
| `audit.audit_type.types[].users` | user reference list, max 500 | The corresponding approvers; the designated-people mode must satisfy business rules. |
| `audit.notification` | object | Notification settings for the approval flow. |
| `audit.notification.admin_notification` | shared notification object | Admin notifications such as pending approval. |
| `audit.notification.user_notification` | shared notification object | User notifications such as the approval result. |
| `auto_generate` | object | Container for the frontend "auto generate" setting. |
| `auto_generate.enabled` | Y/N | The frontend can save and echo it, but domain enforcement was not located in this review; do not promise it produces any business result. |

## jump_logic_config: show questions after a choice

Field prefix: `definition.config.jump_logic_config`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `rules` | list, max 500 | Rules defined by priority and question relationships. |
| `rules[].id` | scalar | Rule identifier. |
| `rules[].priority` | integer | Rule priority. |
| `rules[].source_item_key` | string | item_key of the source choice question. |
| `rules[].when` | object | Condition on the source answer. |
| `rules[].when.operator` | `in` / `not in` | The selected values intersect / do not intersect the condition values; the actual code is `not in` with a space. |
| `rules[].when.values` | scalar list, max 500 | Option values of the source question, **not item_key**; old example comments are misleading. |
| `rules[].actions` | list, max 100 | Actions run when the condition holds. |
| `rules[].actions[].type` | `show_items` | The action currently executed is showing questions; do not infer other actions from open fields. |
| `rules[].actions[].item_keys` | string list, max 500 | item_key of the target questions to show; can point to a whole question group. |

The supported source types are actually `radio/select/gender/checkbox`. Targets must come after the source and cannot be layout questions, private questions or blacklist/whitelist condition questions; a source inside a question group can control only later questions in the same group. Target questions that are not shown are not validated, and malicious answers to them are discarded. Duplicate-check dimensions also need a required question that is not a jump target to carry them.

## share_config: portal, channels and sharing

Field prefix: `definition.config.share_config`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `portal_publish` | object | Whether to show the form on the organization portal; not the same as formally publishing the form. |
| `portal_publish.enabled` | Y/N | Portal display switch. |
| `portal_publish.participation` | object | Portal participation policy. |
| `portal_publish.participation.login_required` | Y/N | Whether portal participation requires login. |
| `portal_publish.images` | object | Portal images per device. |
| `portal_publish.images.mobile` | shared image object | Mobile portal image; different from the form header image. |
| `portal_publish.images.mobile.enabled` | Y/N | Whether to use the mobile portal image. |
| `portal_publish.images.mobile.width` | number | Mobile portal image width info. |
| `portal_publish.images.mobile.height` | number | Mobile portal image height info. |
| `portal_publish.images.pc` | shared image object | Desktop portal image. |
| `portal_publish.images.pc.enabled` | Y/N | Whether to use the desktop portal image. |
| `portal_publish.images.pc.width` | number | Desktop portal image width info. |
| `portal_publish.images.pc.height` | number | Desktop portal image height info. |
| `channel` | list, max 100 | Channel list; saving also syncs form_channel. Every channel must provide `code/title/link/short_link`; "the field must be present" and "the value may be an empty string" are two different things, and omitting a field fails validation (preflight). An empty string is not a deliverable link; after saving, read back and verify the real link before delivering it. |
| `channel[].id` | integer, read back/existing reference | Identifier of an existing channel; never fabricate one for a new channel. |
| `channel[].team_id` | integer, read back | Organization ownership; do not use it to create across organizations. |
| `channel[].code` | string | Stable channel code. |
| `channel[].title` | string | Channel name. |
| `channel[].parent_id` | integer | Parent channel reference; follow the currently supported relationships. |
| `channel[].link` | string, **field must be present** | Channel long link. For a new channel with no link yet, pass `""`; when editing an existing channel, keep the real value; never deliver example links. |
| `channel[].short_link` | string, **field must be present** | Channel short link, same rules as `link`; generation and permissions are decided by the server. |
| `channel[].enabled` | Y/N | Channel enabled state. |
| `social_media` | object | Social sharing settings. |
| `social_media.enabled` | Y/N | Master switch for social sharing. |
| `social_media.media` | dynamic object | Saved by platform code; current examples are whatsapp/weixin/facebook/threads. |
| `social_media.media.*` | object | Config for one platform; dynamic keys must still follow the nested structure. |
| `social_media.media.*.enabled` | Y/N | Whether this platform is enabled. |
| `social_media.media.*.title` | string | Platform/share display title; follow the control's semantics. |
| `social_media.media.*.link` | URL string | Share link; must be a real, verified address. |
| `social_media.media.*.image` | shared image object | Share image; the final display is decided by the platform. |
| `social_media.media.*.content` | string | Share body text. |
| `social_media.media.*.subject` | string, frontend extension | Share subject saved by the frontend; the Schema accepts it through an open object; not every platform shows it. |
| `iframe` | object | Website embed settings. |
| `iframe.enabled` | Y/N | Whether embedding is enabled. |
| `iframe.channel` | list, max 100 | Embed code saved per channel. |
| `iframe.channel[].code` | string | The corresponding channel code. |
| `iframe.channel[].source` | HTML string | iframe code; `__CHANNEL_LINK__` is replaced with the real channel link; never treat the template placeholder as a go-live result. |

## ext_config: WeChat environment and sharing

Field prefix: `definition.config.ext_config`

| Field | Type/limit | Purpose/boundary |
| --- | --- | --- |
| `weixin` | object | WeChat extension features; constrained by the organization's WeChat capability switch. |
| `weixin.only_weixin_environment` | Y/N | Allow access/submission only inside the WeChat environment. |
| `weixin.share` | object | WeChat share card config; separate from the social platform list. |
| `weixin.share.title` | string | Share title; the current Request limits it to max 20 characters. |
| `weixin.share.content` | string | Share summary; the current Request limits it to max 20 characters. |
| `weixin.share.image` | shared image object | WeChat share image; use a real upload reference. |
| `*` | open JSON | Extension nodes that are not explicitly registered pass only the structure budget validation; there is no per-node enforcement contract in this review, so you cannot add arbitrary features. |

