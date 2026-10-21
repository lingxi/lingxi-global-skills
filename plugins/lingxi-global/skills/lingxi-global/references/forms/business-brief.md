# Business discussion and confirmation

This stage produces the business definition, which then goes to [create execution](create-and-publish.md). Organize the conversation around what the user has already provided and the facts still needed; when necessary, query MCP capabilities read-only in advance so you do not promise unsupported features.

By default **ask everything in one round**: merge the missing key facts into a single question, and for anything that can reasonably be preset, give the preset value directly so the user edits it instead of answering item by item. Follow up with another round only when the user's answer opens a new necessary branch (e.g. ask about amount rules only after charging is confirmed). Do not ask one question at a time, and do not interrogate the user to fill every row of the table below; the table is the agent's checklist, not a question script.

## Fast lane

When the user only wants a simple data collection form and has said what to collect (e.g. "make a registration form collecting name and mobile phone"), skip the full discussion: create it directly with Traditional Chinese source text, name and mobile phone (手提電話) required, no charge, no quota, and no sensitive data collected; say in one sentence what will be created and what was defaulted, and execute once confirmed. If only the title is missing, ask only for the title.

Once activity registration, charging, quota, multi-person registration, header image, translations or notifications come into scope, return to the full flow — these all affect actual participation, charging or fulfillment and cannot be rushed through the fast lane.

## Business definition

This is the agent's concise internal record linking discussion to execution, not a technical form for the user to fill in. Record only what is relevant, and distinguish user facts, accepted presets, delegated drafting scope and open items.

| Topic | What to settle |
| --- | --- |
| Purpose and audience | Ordinary data collection or activity registration; purpose; who participates and eligibility |
| Time and participation mode | Activity dates, per-session time slots, location/participation mode; list the registration open period separately. Ordinary forms need only the applicable open period |
| Fees and quota | Whether to charge, decided amounts and rules, quota and multi-person registration method; do not automatically equate a "paid activity" with online payment collection |
| Content and materials | Name, introduction, questions and necessary options, header image source or design requirements, language, the outcome the success page should convey |
| Business settings | Required-ness, repeat registration, collect into contacts / keep or overwrite existing data, notification recipients and timing, and other settings with real impact |
| Execution scope | Plan only, draft only, or create and publish; whether it covers any necessary submit for approval, chosen translations and related notification impact |

Key facts that are not provided and affect actual participation, charging, eligibility or fulfillment must be asked of the user; never treat specific times, venues, fees, instructors or arrangements as routine presets. Missing items can be asked together; do not re-ask what the user has clearly answered. When only a basic draft is requested, key gaps may remain, but state that it is not yet ready for formal registration.

## Copy and presets

The user can delegate the title, introduction, questions, translations and layout to the agent; within the authorized scope, do not require paragraph-by-paragraph review. For content not delegated for drafting, give a reviewable proposal. Leave technical enums, nodes and date rules to the execution stage.

You may propose reasonable presets together, e.g. source and translation languages, name and mobile phone questions, required-ness, and success page text. For a new form with no language specified, suggest Traditional Chinese source text with an English translation; the chat language does not determine the page language. The header image can be designed, taken from an existing image, or left out; do not turn an optional image into something the user must answer; when the user asks for an image, do not omit it on your own.

For contact collection you may suggest collecting into contacts while keeping existing data, and enable it once accepted; overwriting existing data requires an explicit choice. Do not collect sensitive data such as ID documents, health or home address by default, and do not enable payment or send messages on your own. The success page states the actual submission result; never phrase "submitted" as approved or paid. Hong Kong Traditional Chinese copy uses 「電郵、手提電話、參加者、聯絡人」; keep official names and source text the user specified.

## One business confirmation

Use a concise summary or a "Setting | Content" table to explain the core facts, the data actually collected and which items are required, important defaults, fees, contact policy, notification impact and execution scope. For delegated body text, describe its theme and purpose without pasting it in full; show the full text when the user asks to review it. Show candidate images in this round only when they affect the decision; do not repeat already-confirmed materials.

By default, follow this skeleton and give actual values item by item so confirmation summaries stay consistent across sessions; omit rows that don't apply instead of leaving placeholders:

- Name and purpose
- Time and location or participation mode (list each session for activities; the open period for ordinary forms)
- Fees and quota
- What data is collected, and which is required
- Contact collection policy (collect and keep existing data / overwrite / don't collect)
- Language (source text and translations)
- Notification recipients and timing triggered by this batch
- How far this batch executes (plan only / draft only / create and publish; whether it includes any necessary submit for approval)

You can phrase it naturally: "I've defaulted these to …; the body is organized from what you provided, and once you confirm I'll create and publish it. If your organization requires approval, I'll submit it for approval first." Also list the notification recipients and timing this batch may trigger; when notification behavior is unclear, find out first, and never stretch a general go-live authorization to cover mass messaging.

Default execution scope is tiered by risk: an ordinary data collection form can be created and published directly once the user confirms this batch; activity registration, paid, or payment-related forms stop at draft by default — hand over the admin editor link so the user can look at it personally, then get explicit authorization to publish. Mistakes in these forms cost real participants and real money. When the user explicitly asks to publish directly, do as asked without repeatedly trying to dissuade them.

Once the user explicitly confirms this batch, technical execution can save, verify and, when the status permits, publish in one continuous run; authorization that explicitly covers this content and these actions remains valid. User silence, or the agent unilaterally announcing "I'll continue if there are no problems", is not user consent; the user explicitly saying "continue if there are no problems" within the stated batch scope is conditional authorization — continue once the conditions are verified. When the user only says "make an activity for me" and the core facts and scope are undecided, finish this stage first. If you find new fees, extra unpublished changes or other material changes during execution, confirm only the differences.
