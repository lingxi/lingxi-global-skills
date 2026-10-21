# payment_config: payments, discounts, stock and receipts

For general conventions see [form-definition.md](form-definition.md). All config paths live under `definition.config.payment_config`. Use a `payment` question to define real tiers, and check the organization's payment permission, available channels and receipt templates; turning a switch on does not prove payment succeeds. Submit business amounts in major currency units (for example HK$100.50, not cents); the server converts them to cents for storage, and every read tool returns major units too, so never copy stored amounts.

## Discounts and stock

Field prefix: `definition.config.payment_config`

| Field | Type / limits | Purpose / boundaries |
| --- | --- | --- |
| `discount` | Object | Payment tier discount. |
| `discount.enabled` | Y/N | Whether to use discounted prices. |
| `discount.allow_multiple_type` | Object | Reserved container for multiple discount types. |
| `discount.allow_multiple_type.enabled` | Y/N | Only one discount type is supported today; keep this off and do not promise stacked discounts. |
| `discount.discount_type` | List, max 20 | Config for each discount; the current implementation uses early-bird pricing. |
| `discount.discount_type[].code` | `early_bird` | The currently supported early-bird discount code; other types are reserved. |
| `discount.discount_type[].title` | String | Discount name. |
| `discount.discount_type[].source_type` | String | Business source code of the discount; follow the live template. Not the question's source_type. |
| `discount.discount_type[].quota` | Integer/null, not enabled | Business validation on save always clears it to null; cannot be used to cap the discount quota. |
| `discount.discount_type[].period` | Object | Discount validity period. |
| `discount.discount_type[].period.start_at` | Date/datetime | Required when the discount is enabled; must be parseable. |
| `discount.discount_type[].period.end_at` | Date/datetime | Required; actual save validation allows it to equal the start; when the registration-period limit is on, it cannot go beyond the registration period. |
| `discount.discount_type[].discount_mode` | Object | Ratio or fixed-reduction method. |
| `discount.discount_type[].discount_mode.selected` | `ratio` / `fixed` | Percentage discount or fixed-amount reduction. |
| `discount.discount_type[].discount_mode.mode` | List of mode candidates, max 20 | Name and range of each method; see the shared mode table. |
| `discount.discount_type[].price_mode` | Object | Whether the discount applies to all tiers or per tier. |
| `discount.discount_type[].price_mode.selected` | `all` / `single` | One setting for all tiers, or per-tier settings. |
| `discount.discount_type[].price_mode.all` | Object | Discount value shared by all tiers. |
| `discount.discount_type[].price_mode.all.ratio` | Number 0.1..100 | **Percentage of the original price payable**; 90 means pay 90% of the original price, not a 90% reduction. |
| `discount.discount_type[].price_mode.all.fixed` | Non-negative amount in major currency units (not cents), max 2 decimals | **Reduction amount**; original price 100 with fixed=20 means pay 80; 0 means no reduction. Cannot exceed the corresponding original price. |
| `discount.discount_type[].price_mode.single` | List, max 500 | Per-tier discounts; at least one tier must have a valid value for the current discount method. |
| `discount.discount_type[].price_mode.single[].value` | String | Tier value from the payment question's products; if no matching tier is found, no price reduction is forced. |
| `discount.discount_type[].price_mode.single[].ratio` | Number 0.1..100 | Payable percentage for this tier. |
| `discount.discount_type[].price_mode.single[].fixed` | Non-negative amount in major currency units (not cents), max 2 decimals | Reduction for this tier; 0 means no reduction; cannot exceed the original price. |
| `discount.discount_type[].price_mode.mode` | List of mode candidates, max 20 | all/single options. |
| `discount.discount_type[].eligibility` | Object | Container for discount eligibility config. |
| `discount.discount_type[].eligibility.selected` | `unlimited` | Only "no restriction" is supported today; whitelist/field are reserved — do not promise member-only or field-based discounts. |
| `discount.discount_type[].eligibility.mode` | List of mode candidates, max 20 | Eligibility candidates; a reserved option appearing here does not mean it can be executed. |
| `product_quantity` | Object | Per-tier stock and per-purchase limit. |
| `product_quantity.enabled` | Y/N | Whether to limit tier stock; when off, no purchase limit is enforced from it. |
| `product_quantity.stocks` | List, max 500 | Tier stock rows. |
| `product_quantity.stocks[].value` | String | Matches the payment question's products[].value. |
| `product_quantity.stocks[].quantity` | Non-negative integer/null | Stock quantity; 0 means sold out; empty means not set separately, and interacts with the overall quota rules. |
| `product_quantity.stocks[].purchase_limit` | Positive integer/null | Maximum per purchase; the frontend normalizes empty/0 to unlimited; business rules decide the final result. |

Field prefix: `definition.config.payment_config.discount.discount_type[].discount_mode.mode[]`, `definition.config.payment_config.discount.discount_type[].price_mode.mode[]`, `definition.config.payment_config.discount.discount_type[].eligibility.mode[]`

| Field | Type | Purpose |
| --- | --- | --- |
| `code` | String, candidate | Mode code; chosen by the corresponding selected. |
| `title` | String, candidate | Mode name. |
| `min` | Number/null, candidate | Lower bound for this method; you cannot loosen server rules by editing the candidates. |
| `max` | Number/null, candidate | Upper bound for this method. |

For ratio and fixed amount, only the value matching the current selected is used. Save-time validation of the discount period follows the actual Validator; do not treat the Schema comment "must be later than now" as an enforced check. quantity/purchase_limit/discount on the payment question are echoed/derived information; this section is the save source for stock and discounts.

## Electronic receipts

Field prefix: `definition.config.payment_config.receipt`

| Field | Type / limits | Purpose / boundaries |
| --- | --- | --- |
| `enabled` | Y/N | Whether to generate an electronic receipt after payment completes. A draft may enable it before choosing a template; publishing requires the template requirements to be met. |
| `templates` | Object | Receipt template candidates and selection. |
| `templates.option` | List, max 200, candidate | Receipt templates currently allowed for the organization. |
| `templates.option[].id` | Real template ID, candidate | Identifier returned by the tool; may be an encoded string. |
| `templates.option[].title` | String, candidate | Template name. |
| `templates.option[].source_template_id` | Real ID, candidate extension | Source template reference returned by the template service; do not use it in place of selected. |
| `templates.option[].variables` | List of open objects, max 500, candidate | Template variables; the table below explains the two structures seen in actual responses. |
| `templates.selected` | Template ID/null | Currently selected template; use the actual candidate identifier; ownership and status must be valid. |
| `receipt_template_id` | Scalar, legacy/Schema field | Not the current selection entry point; the current formatter takes the value from templates.selected and removes this field. |
| `field_mapping` | Object | Mapping of business fields to template variables. |
| `field_mapping.fields` | List, max 500 | Mapping rows. |
| `field_mapping.fields[].code` | String | Business field code, normalized to lowercase on save; follow the candidate fields, e.g. title/receipt_no. |
| `field_mapping.fields[].title` | String | Display name of the business field. |
| `field_mapping.fields[].variable` | String | Target template variable/expression; must match the current template candidates. |
| `field_mapping.fields[].required` | Boolean/scalar | Whether the mapping is required; the formatter casts it to boolean; **not Y/N 1/2** — 2 is cast to true. |
| `field_mapping.fields[].fixed` | Boolean/scalar | Whether the mapping is fixed and not editable; the formatter casts it to boolean; not a fixed amount. |
| `variable_mappings` | Any JSON, legacy field | Allowed by the Schema, but the current formatter removes it; save via field_mapping.fields, and do not claim arbitrary mappings take effect. |
| `issue_condition` | Object | Issuance condition. |
| `issue_condition.selected` | `amount_gte` / `unlimited` | Payment amount reaches a threshold, or no restriction. |
| `issue_condition.mode` | List, max 20 | Config for each condition; keep the order and structure currently returned. |
| `issue_condition.mode[].code` | String | Condition code matching selected. |
| `issue_condition.mode[].title` | String, candidate | Condition name. |
| `issue_condition.mode[].min_amount` | Amount in major currency units (not cents) / null | Minimum payment amount for amount_gte; a payment equal to the threshold qualifies. |
| `issue_condition.mode[].zero_payment` | Y/N | Under unlimited, whether a zero-amount payment gets a receipt. |
| `user_notification` | Shared notification object | Email settings after the receipt is generated; for all nested fields see [Notification nodes](form-config-fields.md#notification-nodes); at send time the recipient is the respondent. |

Field prefix: `definition.config.payment_config.receipt.templates.option[].variables[]`

| Field | Type | Purpose / response differences |
| --- | --- | --- |
| `type` | String, candidate | system/variable type in the form-detail projection. |
| `value` | String, candidate | Variable value/default expression. |
| `label` | String, candidate | Variable display name. |
| `var` | String, candidate | Variable key in the form-detail projection. |
| `id` | String, candidate | Variable record ID in the template-management response. |
| `receipt_template_id` | String, candidate | Template the variable belongs to. |
| `variable_key` | String, candidate | Variable key in the template-management response; equivalent to var in the detail. |
| `built_in` | Boolean, candidate | Whether it is a built-in system variable. |
| `sort` | Integer, candidate | Display order of template variables. |
| `*` | Open JSON | Open field in the Schema for template variable elements; the rows above are the known fields the service actually produces. Do not design your own variable structure. |

Template candidates/variables are read-back metadata; do not copy another organization's template to create receipts. Verify the receipt email and receipt generation separately; email enabled does not mean an email has been sent.

## Payment methods and channels

Field prefix: `definition.config.payment_config.payment_method`

| Field | Type / limits | Purpose / boundaries |
| --- | --- | --- |
| `enabled` | Y/N | Switch for payment method settings; not the e-receipt switch — an old Schema comment makes this easy to confuse. |
| `payment_currency` | List, max 50 | Currency config/candidates; the current Enum is HKD. The currency on form orders is a different business field. |
| `payment_currency[].value` | String | Currency code, e.g. HKD; use the casing returned live. |
| `payment_currency[].label` | String, candidate | Currency name. |
| `payment_currency[].symbol` | String, candidate | Currency symbol. |
| `payment_channel` | Object | Payment channels available to the organization. |
| `payment_channel.option` | Grouped list, max 200, candidate | Organization channels that can be selected; do not add candidates yourself. |
| `payment_channel.selected` | Grouped list, max 200 | Currently selected channel groups and their actual sub-channels. |
| `payment_mode` | Object | Payment method; not the same as the payment platform. |
| `payment_mode.option` | List, max 100, candidate | Payment methods currently allowed by the business. |
| `payment_mode.option[].value` | String, candidate | Payment method code. |
| `payment_mode.option[].label` | String, candidate | Payment method name. |
| `payment_mode.selected` | List, max 100 | Payment methods actually enabled now. |
| `payment_mode.selected[].mode` | String | Actual payment method code on save; follow the organization's candidates. |
| `payment_mode.selected[].value` | Scalar, compatibility/candidate | Method value in some responses; do not substitute it for mode. |
| `payment_mode.selected[].label` | String, candidate | Payment method display name. |
| `payment_alert` | Object | Prompt shown before submission / payment redirect. |
| `payment_alert.enabled` | Y/N | Whether to pop up the payment reminder. |
| `payment_alert.message` | String | Reminder text; must match the organization's real payment collection arrangements; do not copy an example organization's name. |

Field prefix: `definition.config.payment_config.payment_method.payment_channel.option[]`, `definition.config.payment_config.payment_method.payment_channel.selected[]`

| Field | Type / limits | Purpose |
| --- | --- | --- |
| `value` | String | **The outer level is only the platform/channel group code**, not the payment_channel_id used for orders. |
| `label` | String | Group name. |
| `logo` | String/null | Platform logo display value. |
| `children` | List, max 200 | The organization's actual payment channels in the group. |
| `children[].value` | Real channel ID | **The payment_channel_id used for orders**; never substitute the outer value. |
| `children[].label` | String | Actual channel name. |
| `children[].logo` | String/null | Actual channel logo. |

The admin side saves the actual payment methods; the public (C-end) side projects cash/POS as a "pay on site" scenario, so never save the client-side display group back as an actual method. For currency, method and channel, use the values the tools return for the current organization.

