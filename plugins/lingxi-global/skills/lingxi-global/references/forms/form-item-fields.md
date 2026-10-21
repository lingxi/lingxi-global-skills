# items: Fields for the 30 Question Types

For general conventions, see [form-definition.md](form-definition.md). Question config lives in `definition.items[]`; question answers are not configured here, and for answers when submitting on behalf (admin submission) read [submit.md](submit.md) separately. The same-named common_config here is **question-level** config; do not mix it up with `definition.config.common_config`.

## Common question fields

Field prefix: `definition.items[]`

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `item_key` | string, required, max 50 chars | Stable question key, unique across the whole form; skip logic, grouping, translations and answer linkage all use it. Must not use a system reserved key (see below). |
| `group_code` | string/null, max 50 chars | item_key of the question group it belongs to; empty when not in a group; not nested children. |
| `sort` | integer >=0, required | Question order; affects display and the ordering that skip logic relies on. |
| `type_code` | current question type code, required | One of the 30 registered question types in the table below. |
| `source_type` | must equal the question type's template | Underlying control type; do not infer it from type_code alone. |
| `category` | must equal the question type's template | contact/common/advanced/layout category; not a form category ID. |
| `label` | string, max 255 chars | Question title; whether required depends on the question type; some layout types allow empty. |
| `description` | string/null | Question description/rich text; separate from the form details. |
| `hidden` | Y/N, required | UI flag: 1 hidden, 2 shown. |
| `private` | Y/N, required | 1 = private question, not filled in by regular respondents; the server handles admin-console editing separately; not just CSS hiding. |
| `validation_message` | string/null, max 1000 chars | Error message for format/required validation. |
| `predefined_value` | per question type | Prefilled value; see the dedicated table below; not an answer example. When unset, keep the template's empty value. |
| `placeholder` | string/null, max 255 chars | Input placeholder; compound questions such as name also have dedicated placeholder fields. |
| `customized_key` | string/null, max 50 chars | Custom field key for business identification; does not replace item_key. |
| `rule_config` | array/object, must be present | Question validation rules; use only rules the question type supports. Layout/group questions may use an empty array. |
| `common_config` | array/object, must be present | Question display and type-specific behavior. |
| `option` | array/object, must be present | Type-specific content such as options, matrix or payment tiers; with no type-specific content, keep the template's empty array. |

`item_kind/ai_note/answer_example/answer_text_example` are descriptive/read-only fields of the definition tools and **must not be submitted**. System questions (device, IP, duration, approval, etc.) are maintained by the server; do not copy system questions the frontend fills in into MCP requests.

A regular question's `item_key` must not use a system reserved key: `duration`, `device`, `os`, `browser`, `region`, `ip`, `lingxi_openid`, `team_openid`, `channel`, `params`. A regular question using one of these keys would be treated as a system question and hidden or overwritten on save; validation (preflight) rejects it outright. Give new questions stable business names, such as `q_network_ip`, `q_channel_source`; do not casually rename the keys of existing regular questions, to avoid breaking skip logic, translations and links to past answers.

When the current MCP calls `ItemPayloadValidator`, some internal template keys may be omitted, but the three containers rule_config/common_config/option must be present, unknown keys are still rejected, and business dependencies must still be satisfied. When assembling, prefer starting from the full template fetched for this task and changing only the necessary values; do not guess the structure by trimming it down.

## Question type index

| type_code | source_type | category | Purpose / dedicated section |
| --- | --- | --- | --- |
| `username` | input | contact | Name, split name, title (honorific); see Name. |
| `mobile` | input | contact | Mobile phone, area code; see Mobile. |
| `landline_phone` | input | contact | Landline phone; uses the common required rule and collect into contacts. |
| `email` | input | contact | Email; uses regex_format and collect into contacts. |
| `address` | address | contact | Area, street, coordinates or manually entered address. |
| `birthday` | date | contact | Birthday; supports year-month-day / year-month / month-day. |
| `gender` | select | contact | Gender single choice; choice config and collect into contacts. |
| `id_card` | input | contact | ID document type and number. |
| `input` | input | common | Single-line text, required/length. |
| `textarea` | textarea | common | Multi-line text, required/length. |
| `radio` | radio | common | Single choice; options/images/"Other"/assigned values. |
| `checkbox` | checkbox | common | Multiple choice; length limits the number of selections. |
| `select` | select | common | Single- or multi-select dropdown. |
| `cascader` | select | advanced | Cascading option tree of up to three levels. |
| `number` | number | common | Number; decimal places and value range. |
| `date` | date | common | Date/datetime, date_range. |
| `time` | time | common | Hour-minute or hour-minute-second. |
| `file_upload` | file_upload | common | File type, size, count. |
| `image_upload` | file_upload | common | Images; camera-only setting. |
| `signature` | signature | advanced | Handwritten signature; pen style. |
| `field_group` | field_group | advanced | Repeatable question group; child questions still go in the flat items list. |
| `matrix` | matrix | advanced | Text entered by row and column. |
| `matrix_choice` | matrix_choice | advanced | Single/multiple choice per row. |
| `sort` | select | advanced | Ranking choice; not the frontend's ranking code. |
| `rating` | rating | advanced | 1..5 rating with icons. |
| `nps` | nps | advanced | 0..10 likelihood-to-recommend score. |
| `payment` | payment | advanced | Preset payment tiers / custom amount. |
| `description` | description | layout | Display text; no answer. |
| `divider` | divider | layout | Divider line; no answer. |
| `page` | page | layout | Page break and previous/next page button text. |

## Shared display, collection and rules

The following are common suffixes; which question types they apply to is determined by the template and the notes below. Do not add every rule to every question.

Field prefix: `definition.items[].common_config`

| Field | Type | Purpose |
| --- | --- | --- |
| `show_label` | Y/N | Show the question title. |
| `label_position` | string | Title position; the template value is top; use the current client's candidates. |
| `show_description` | Y/N | Show the description body. |
| `show_placeholder` | Y/N | Show the input placeholder. |
| `collect_to_contact` | object | Question-level collection into contact attributes. |
| `collect_to_contact.enabled` | Y/N | Whether to collect this question's answer; also subject to the global switch in submitted_config. |
| `collect_to_contact.anchor` | string | Contact field anchor; for fixed fields keep the template value; for custom contact fields use the current organization's candidates. |
| `collect_to_contact.can_change_anchor` | Y/N, candidate | Whether the designer allows changing the anchor; does not relax backend field-ownership validation. |

Collect into contacts supports the 8 contact question types; the regular question types that can be collected into custom contact fields are `input/textarea/date/time/number/radio/checkbox/select/file_upload/image_upload`. Do not add this object to other question types. When collection is enabled, `anchor` must still be a custom contact field that is actually available in the current organization. The fixed anchor for name is username; the default Hong Kong phone area code must still be looked up from live candidates.

Field prefix: `definition.items[].rule_config`

| Field | Type/limits | Purpose/applies to |
| --- | --- | --- |
| `required` | object | Common required container; the field_group and layout question templates do not have it. |
| `required.enabled` | Y/N | Whether required; forced to 1 for payment. |
| `length` | object | Length/count/value range, where the template supports it. |
| `length.enabled` | Y/N | Whether the range is enabled. |
| `length.min` | non-negative integer or number/null | Text length for username/input/textarea; number of selections for checkbox/sort; minimum value for number. |
| `length.max` | non-negative integer or number/null | Corresponding upper bound, min<=max; for number, the current MCP caps range values at 10000. |
| `regex_format` | object | Format validation for mobile/email/id_card. |
| `regex_format.enabled` | Y/N | Whether to run the corresponding format validation. |
| `regex_format.regex` | string | The template's built-in validator identifier, such as mobile/email/id_card; do not take it to mean arbitrary expressions are supported. |
| `regex_format.selected` | string | Currently selected validator identifier. |
| `regex_format.default` | string, candidate | Default validator identifier. |
| `date_range` | object | Date range for date; not the form's open period. |
| `date_range.enabled` | Y/N | Whether to restrict selectable dates. |
| `date_range.start` | date string/null | Earliest date; must be parseable. |
| `date_range.end` | date string/null | Latest date; must not be earlier than start. |

## Name (username)

Field prefix: `definition.items[].common_config`

| Field | Type/values | Purpose |
| --- | --- | --- |
| `collect_mode` | object | Name structure and the placeholder for each input box. |
| `collect_mode.default` | `name_zh/lastname_firstname_zh/name_en/lastname_firstname_en` | Chinese/English full name, or surname and given name separately; the frontend's first_last_name_* maps to the backend's lastname_firstname_*. |
| `collect_mode.choices` | candidate list | Available name collection structures. |
| `collect_mode.choices[].code` | string | Name structure code. |
| `collect_mode.choices[].title` | string | Structure name. |
| `collect_mode.placeholder` | list/legacy object | Placeholders for each name part; the current frontend saves a code/title list. |
| `collect_mode.placeholder[].code` | `name_zh/lastname_zh/firstname_zh/name_en/lastname_en/firstname_en` | The corresponding name part. |
| `collect_mode.placeholder[].title` | string | Placeholder text for that input box. |
| `name_title` | object | Title (honorific) settings. |
| `name_title.enabled` | Y/N | Whether to show the title. |
| `name_title.placeholder` | string, max 255 chars | Prompt for choosing a title. |
| `name_title.choices` | dynamic candidate list, **must be submitted** | Titles from the current organization/dictionary; do not fill them in from memory. When not using titles, still keep the whole `name_title` with `enabled=2` and pass `choices: []`; do not omit it. |
| `name_title.choices[].code` | string | Title code. |
| `name_title.choices[].title` | string | Title name. |
| `name_title.choice_i18n` | object, candidate metadata | Translation source for the title candidates; keep and submit it per the template; it is not an extra field that causes validation (preflight) to fail. |
| `name_title.choice_i18n.sources` | list, candidate metadata | Dictionary/enum sources. |
| `name_title.choice_i18n.sources[].type` | string | E.g. category; not a collected value. |
| `name_title.choice_i18n.sources[].code` | string | E.g. form_config_dictionary_nametitle; do not modify it to create custom titles. |

Edit name questions from the live full template; do not casually delete config. `allowMissingTemplateKeys` tolerates only a few missing internal template keys; `name_title` and its `choices` are not among them, and the server does not fill in defaults for omitted fields. When validation (preflight) reports an error, first check the error path for a missing field; do not jump to the conclusion that you sent an extra description or translation source.

## Mobile (mobile)

Field prefix: `definition.items[].common_config`

| Field | Type/values | Purpose |
| --- | --- | --- |
| `area_code` | object | International dialing code settings. |
| `area_code.enabled` | Y/N | Whether to show/use the area code. |
| `area_code.default` | area code string from the candidates | Usually `+852` for Hong Kong; the backend template's static example is +86; you must change it to an actual value from this query's candidates. |
| `area_code.choices` | dynamic list, candidate | Get the real area codes and validation rules from the current definition options. |
| `area_code.choices[].code` | string | Area code value. |
| `area_code.choices[].title` | string | Country/region name. |
| `area_code.choices[].flag` | string, candidate | Flag display. |
| `area_code.choices[].length` | number, candidate | Mobile number length rule. |
| `area_code.choices[].regex` | string, candidate | Mobile format rule for that region; not for setting an arbitrary regex. |

Landline (landline_phone) has no separate area code object; email (email) uses the built-in email format validation. Everything else for both follows the shared fields.

## Address (address), Birthday (birthday), ID document (id_card)

The shared candidate shape of collect_mode is below. It applies only to the three question types in this section and to the collect_mode of date/time; the codes differ by question type.

Field prefix: `definition.items[].common_config.collect_mode`

| Field | Type | Purpose |
| --- | --- | --- |
| `default` | string | Currently selected collection mode. |
| `choices` | candidate list | Collection modes available for this question. |
| `choices[].code` | string | Backend mode code. |
| `choices[].title` | string | Mode name. |

Address modes are `area/location/address`: area picker, coordinates and manual entry respectively. Current MCP validation of a non-empty prefill accepts only manual entry `{address: "..."}`; do not assume an area prefill will pass just because the template lists all the area fields.

Field prefix: `definition.items[].common_config` (address)

| Field | Type | Purpose |
| --- | --- | --- |
| `allow_street` | object | Additional street address after area selection. |
| `allow_street.enabled` | Y/N | Whether to collect a detailed street address. |
| `allow_street.placeholder` | string | Street input placeholder. |

Birthday modes are `ymd/ym/md`, meaning year-month-day / year-month / month-day.

Field prefix: `definition.items[].common_config` (birthday)

| Field | Type | Purpose |
| --- | --- | --- |
| `calendar` | object | Birthday calendar. |
| `calendar.default` | `gregorian/lunar` | Current calendar; use the template candidates; the lunar candidate is hidden by default. |
| `calendar.choices` | candidate list | Available calendars. |
| `calendar.choices[].code` | string | Calendar code. |
| `calendar.choices[].title` | string | Calendar name. |
| `calendar.choices[].hidden` | Y/N | Whether this candidate is hidden; 1 = hidden. |

Field prefix: `definition.items[].common_config.collect_mode` (id_card)

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `selected` | non-empty code list | Enabled ID document types; currently built-in id_card/passport/id_card_hk; for custom types use the live organization candidates. |
| `custom_enabled` | Y/N | Whether the current organization's custom ID document types are allowed; does not mean arbitrary strings skip validation. |
| `choice_i18n` | object, candidate metadata | Translation source for ID document types. |
| `choice_i18n.sources` | list, candidate metadata | Built-in and organization-custom sources. |
| `choice_i18n.sources[].type` | string | E.g. enum. |
| `choice_i18n.sources[].code` | string | Source codes such as form_identity_document_type/contact_custom_document_type. |

The ID document default must be within selected and be a real, answerable code. Non-empty prefills for birthday and ID document must also match the current collect_mode/calendar.

## Choice, dropdown and ranking

Applies to radio/checkbox/select/gender. sort uses only the template's allow_switch_type and option list; do not add image/assigned-value common_config to it.

When there are more than 5 options (6 or more), prefer the dropdown (select) question `select`, set `type_mode=single/multiple` per the original requirement, and keep all options, to reduce page length; if the user explicitly asks for options laid out flat, follow that.

Field prefix: `definition.items[].common_config`

| Field | Type/limits | Purpose/applies to |
| --- | --- | --- |
| `allow_switch_type` | candidate list | Question types that radio/checkbox/select/sort may be switched to in the designer. |
| `allow_switch_type[].code` | string, candidate | Question type code: radio/checkbox/select/sort. |
| `allow_switch_type[].title` | string, candidate | Question type name; not an option title. |
| `type_mode` | `single/multiple` | Single or multiple selection for select/gender/cascader; gender allows only single. |
| `type_mode_option` | object | Metadata for the corresponding selection mode. |
| `type_mode_option.default` | string | Must match type_mode. |
| `type_mode_option.choices` | candidate list | Selection modes allowed for this question. |
| `type_mode_option.choices[].code` | string | single/multiple. |
| `type_mode_option.choices[].title` | string | Name for single/multiple selection. |
| `allow_image` | Y/N | Option image switch for radio/checkbox/select/gender. |
| `allow_customized_text` | object | "Other" text input. |
| `allow_customized_text.enabled` | Y/N | Whether entering "Other" option content is allowed. |
| `allow_customized_text.placeholder` | string | "Other" input placeholder. |
| `option_value` | object | Option assigned value and limit settings. |
| `option_value.enabled` | Y/N | Whether option assigned values are enabled. |
| `option_value.limit_enabled` | Y/N | Whether option quantity limits are enabled. |
| `option_value.assigned` | list | Per-option quantity/limit settings; use per the template. |
| `option_value.assigned[].value` | string | The corresponding value in option.choices. |
| `option_value.assigned[].quantity` | number/empty | The frontend maps assignedValue here; it is actually the option's assigned value; the field name quantity is misleading. |
| `option_value.assigned[].limited` | number/empty | The frontend maps assignedLimit here; it is actually the option's quantity limit; not a Y/N switch. |

Field prefix: `definition.items[].option`

| Field | Type/limits | Purpose/applies to |
| --- | --- | --- |
| `prompt` | string/null, max 1000 chars | Selection prompt; the sort template has no such field, but the current option rules accept it. |
| `choices` | list, 1..500 | Options; value is unique within this list. |
| `choices[].name` | string, required, max 1000 chars | Option name shown to respondents. |
| `choices[].value` | string, required, max 50 chars | Stable option value; answers and skip logic use it. |
| `choices[].customized_key` | string/null | Option custom key. |
| `choices[].image_url` | string/null, max 2048 chars, read-back | Display URL of the option image; cannot be treated as a trusted new upload. |
| `choices[].image_id` | trusted file reference | Existing option image; ownership must be verified. |
| `choices[].upload_id` | UUID/null | Trusted reference to a newly uploaded option image. |
| `choices[].assigned_value` | string/null, max 100 chars | Option's assigned value; not the option value. |
| `choices[].assigned_limit` | string/null, max 100 chars | Option limit input value; must match the current business config. |

"Other" uses the stable value `option_other`; do not turn on allow_customized_text without a corresponding option. gender's default options are male/female/secret; extend the text/options according to real needs. For checkbox and sort, length is a count range; switching between single and multiple choice is not just a matter of changing source_type.

## Cascader (cascader)

Field prefix: `definition.items[].common_config`

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `levels` | object | Number of cascade levels. |
| `levels.default` | integer 1..3 | Actual number of levels. |
| `levels.max` | integer, candidate | The current template maximum is 3; do not raise this value to bypass the limit. |

type_mode/type_mode_option use the selection-mode structure above; option.prompt is the selection prompt. Each node in cascader choices **accepts only** name/value/sub_choices; do not add regular option images or customized_key.

Field prefixes: `definition.items[].option.choices[]`, `definition.items[].option.choices[].sub_choices[]`, `definition.items[].option.choices[].sub_choices[].sub_choices[]`

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `name` | string, required, max 1000 chars | Option name at this level. |
| `value` | string, required, max 50 chars | Stable value of this node. |
| `sub_choices` | list, must be present | Next-level nodes; [] for a leaf; actual nesting must not exceed levels.default. |

## Number, date, time

Field prefix: `definition.items[].common_config` (number)

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `decimals` | integer 0..8 | Decimal places; 0 means integer. |

For number, length.min/max is the value range; the current MCP limits range values to 0..10000; do not infer support for negative or arbitrarily large values from the frontend's numeric input.

date's collect_mode.default is `ym/ymd/ymdhi/ymdhis`; time's is `hi/his`; the candidate choices[].code/title use the shared structure described above. date input formats are Y-m, Y-m-d, Y-m-d H:i, Y-m-d H:i:s respectively; date_range limits the dates. A time answer may contain hour/minute/second, but the current MCP does not support a non-empty predefined_value for it; do not treat the answer shape as prefill config.

## File, image and signature

Field prefix: `definition.items[].common_config` (file_upload/image_upload)

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `max_size` | integer 1..20 | Per-file size in MB; some frontend defaults are 50, but the MCP limit is still 20. |
| `max_file_quantity` | integer 1..100 | Maximum number of files to upload. |
| `storage` | `oss` | Storage specified by the template; do not change it to local or another disk. |
| `media_type` | non-empty list | Allowed file categories. |
| `media_type[].type` | category code | For file_upload: unlimited/doc/image/video/audio/zip; for image_upload: image only. |
| `media_type[].extensions` | string list | Allowed extensions within the category, without dots; must belong to this template's candidates for that category. The template for unlimited is an empty list. |
| `camera_only` | Y/N, image_upload only | Whether to allow only taking a photo, not choosing an existing image. |

Do not mix all extensions into an arbitrary category. A file upload question uploads attachments while the user fills in the form; this is a different scenario from uploading the form header image, option images or agreement attachments.

Field prefix: `definition.items[].common_config.pen_style` (signature)

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `pen_color` | `#` plus 6 hex digits | Signature pen color. |
| `pen_width` | integer 1..20 | Signature pen width. |
| `bg_color` | `#` plus 6 hex digits | Signature canvas background color. |

## Question group (field_group) and matrix

Field prefix: `definition.items[].common_config.allow_add_item` (field_group)

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `enabled` | Y/N | Whether adding repeated group instances is allowed. |
| `min` | integer 1..100/null | Minimum number of instances. |
| `max` | integer 1..100/null | Maximum number of instances; must not be less than min. |

Child questions are still regular elements of items[], pointing to the group's item_key via group_code. Do not nest children or send back filled instances. For multi-person registration, also cross-check against the form's enrollment_type and the payment-linked headcount.

Field prefix: `definition.items[].option` (matrix/matrix_choice)

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `dimensions` | list, 1..500, matrix only | Columns of the fill-in matrix. |
| `dimensions[].name` | string, max 1000 chars | Column name. |
| `dimensions[].value` | string, max 50 chars, unique within the column set | Stable column key. |
| `dimensions[].customized_key` | string/null | Column custom key. |
| `statements` | list, 1..500 | Rows of the matrix. |
| `statements[].label` | string, max 1000 chars | Row name. |
| `statements[].item_key` | string, max 50 chars, unique within the row set | Stable row key. |
| `statements[].customized_key` | string/null | Row custom key. |
| `choice_style` | `single/multiple`, matrix_choice only | Single or multiple choice per row. |
| `choices` | list, 1..500, matrix_choice only | Selectable columns; name/value/customized_key use the option structure above; use other candidate fields only as validation actually allows. |

matrix fill-in answers are organized as "row key → column key → text"; matrix_choice answers as "row key → list of selected column values". Do not write the row/column tree as a sub-list of regular questions.

## Rating (rating) and NPS

Field prefix: `definition.items[].common_config` (rating/nps)

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `show_score_labels` | Y/N, rating template only | Whether to show score labels; the NPS frontend also writes this value but the current MCP does not accept it; see the discrepancy table. |
| `rating_type` | current icon code | rating uses the star_type candidates; nps currently validates only star. |
| `rating_max` | integer 1..5 for rating; fixed at 10 for nps | Maximum score; the NPS answer range is 0..10. |
| `min_ratings_display_text` | string | Text at the low end. |
| `max_ratings_display_text` | string | Text at the high end. |
| `star_type` | candidate list, rating only | Available icon set. |
| `star_type[].code` | string, candidate | star/flag/heart/thumbs-up/user/circle-check-big/lightbulb/smile. |
| `star_type[].title` | string, candidate | Icon name. |

The template's answer examples are not validated against the real range; for example, the rating example is 7 while max-score validation is 5. Do not let an example override the limit.

## Payment tiers (payment)

Field prefix: `definition.items[].common_config` (payment)

| Field | Type/limits | Purpose |
| --- | --- | --- |
| `allow_multi_product` | object | Multi-tier selection setting. |
| `allow_multi_product.enabled` | Y/N | Whether multiple tiers can be selected at once. |
| `allow_customized_price` | object | Custom payment amount. |
| `allow_customized_price.enabled` | Y/N | Whether a custom amount is allowed. |
| `allow_customized_price.key` | `_customized` | Reserved tier value for a custom amount; do not invent other conventions. |
| `quantity_link_enrollment` | object | Links purchase quantity to registration headcount. |
| `quantity_link_enrollment.enabled` | Y/N | Whether attendee count is linked to the number of units purchased; must match the multi-person registration rules. |

Field prefix: `definition.items[].option` (payment)

| Field | Type/limits | Purpose/boundaries |
| --- | --- | --- |
| `products` | list, 1..500 | Set of payment tiers. |
| `products[].label` | string, required, max 1000 chars | Tier name. |
| `products[].value` | string, required, max 50 chars, unique | Stable tier value; discounts, inventory and answers all link to it. |
| `products[].price` | non-negative amount in major currency units (not cents), up to two decimal places | Tier's original price; regular tiers must pass business price validation; the database's value in cents is not returned as-is. |
| `products[].description` | string | Tier description; can be included in the translation catalog. |
| `products[].quantity` | number/null, derived for display | Taken from payment config/inventory; not an independent source for saving inventory. |
| `products[].purchase_limit` | number/null, derived for display | Purchase limit taken from payment config. |
| `products[].discount` | list, derived for display | Discount info the server projects from payment_config; do not create separate discounts here. |
| `products[].discount[].code` | string | Discount code. |
| `products[].discount[].label` | string | Discount name, generated by the system at runtime. |
| `products[].discount[].period` | object | Discount validity period. |
| `products[].discount[].period.start_at` | date/datetime | Discount start. |
| `products[].discount[].period.end_at` | date/datetime | Discount end. |
| `products[].discount[].discount_price` | amount in major currency units | Display of the discount amount computed by the current service; do not assume from the name that it is the final sale price; for the calculation, see PaymentService. |

required.enabled must be 1. For discounts, inventory, receipts and payment methods, see [form-payment-fields.md](form-payment-fields.md). The current MCP does not accept the frontend's extra top-level products[].discount_price field.

Current save logic does not allow a payment tier question and submission approval to be enabled at the same time; do not promise "paid registration followed by approval" just because a static config check passed.

## Description, divider, page

description/divider use only the common title, description and display settings; rule_config/option keep the empty template; they collect no answers. The designer extension subtitle on description is not part of the current MCP template; see the discrepancy table.

Field prefix: `definition.items[].common_config` (page)

| Field | Type | Purpose |
| --- | --- | --- |
| `next_page_text` | string | Next-page button text. |
| `previous_page_text` | string | Previous-page button text. |

## Nested structure of predefined_value

A non-empty prefill goes through separate strict rules; an empty []/null can express "unset". The following is not the full answer interface.

Field prefix: `definition.items[].predefined_value`

| Field | Type/question types | Purpose/limits |
| --- | --- | --- |
| `collect_mode` | string, username/birthday/id_card | Must equal this question's current collection mode. |
| `name_zh` | string, username | Chinese full-name prefill; the key for the currently selected mode. |
| `name_en` | string, username | English full-name prefill; strict validation accepts only collect_mode and the current mode's key; do not guess a split name as a multi-key object. |
| `calendar` | string, birthday | Must equal this question's current calendar. |
| `value` | string, birthday/id_card | Birthday format matches the mode; ID number matches the ID document type. |
| `value[]` | single-element list of option values, gender | Can select only one value from the current choices. |
| `address` | string, address | A non-empty address prefill currently allows only this manual-entry field. |
| `province` | template empty value, address | The template's province/area slot; the current MCP does not accept a non-empty area prefill; do not promise it as a save feature. |
| `city` | template empty value, address | The template's city slot; same restriction as above. |
| `district` | template empty value, address | The template's district slot; same restriction as above. |
| `street` | template empty value, address | The template's street slot; a non-empty prefill cannot set this field directly. |
| `location` | template empty value, address | The template's coordinates slot; non-empty prefill is currently not supported. |

number is a scalar number subject to decimal-place/range validation; input/textarea/email/landline_phone are scalar strings; date is a date string matching the mode. Non-empty prefills for other question types are currently rejected. That mobile can be answered does not mean it can be prefilled in this field.

## Verified frontend/backend discrepancies

The following fields are **written by the current frontend but rejected by MCP strict question validation**; do not copy designer requests verbatim. Their purposes are listed here so the catalog does not miss what the frontend actually configures.

| Frontend field | Purpose | Current MCP handling |
| --- | --- | --- |
| field_group `common_config.instance_title` | Title for repeated instances | Not in the template common_config; rejected. |
| description `common_config.show_subtitle` | Show/hide the description subtitle | Not in the template; rejected. |
| description `option.subtitle` | Description subtitle | The current option allows no keys; rejected. |
| birthday `common_config.collect_mode.enable_lunar` | Lunar calendar switch | Not in the template; rejected; handle the calendar via the calendar candidates. |
| id_card `rule_config.length` | Maximum ID number length | The current template has only required/regex_format; this rule object is rejected. |
| nps `common_config.show_score_labels` | Show/hide score labels | The current NPS template has no such field; rejected. |
| sort `common_config.allow_image` | Image switch for ranking options | The current sort template has no such field; rejected. |
| sort `common_config.option_value` | Assigned values/limits for ranking options | The current sort template has no such object; rejected. |
| file_upload `common_config.camera_only` | Camera flag reused by the frontend | The current file question template has no such field; rejected; image questions support it. |
| payment `common_config.product_quantity` | Inventory setting the frontend sends along | The current question template has no such object; rejected; save it to payment_config.product_quantity. |
| payment `option.products[].discount_price` | Designer's tier discount display value | Not on the current products element allowlist; remove it; do not change prices based on it. |
| file/image `common_config.max_size=50` | Default upload size in some frontend cases | Exceeds the MCP limit of 20 MB; do not use this default. |

These discrepancies only record facts; this documentation task does not change frontend or backend functionality. If the tools are upgraded in the future, re-verify against the updated real templates/validation.

## Sources of truth

Field meanings were verified against how both the backend and the frontend actually read and write them; still get candidate values from the current MCP query.
