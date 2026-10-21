# Edit an existing form or activity

Follow the admin console's single shared draft and whole-version publishing; do not create a separate personal draft or copy the activity. Changing just the title or header image does not go through the creation flow again.

## Read and compare

1. Determine the organization and `form_id`; disambiguate ambiguous names per [query.md](query.md). Read the edit state, revision, edit token and permissions from `get_form_draft`. For a published form, also read the live definition, config and links from `get_form(form_id,include_config=true)`; config extensions require management permission; an unpublished form is not required to have a live definition.
2. Separate this request from pre-existing unpublished changes. Compare title, body, questions, config and activity time slots by business meaning; align questions by stable identifiers and keep differences in order, required-ness and conditions. Exclude timestamps, temporary signed URLs and key order once confirmed to have no business impact; mark content you don't understand or that is missing as unverified, never as identical.
3. When attribution is needed, read definition changes with `get_form_changed_logs(form_id,page,page_size)`, paging by `list/meta`; do not extend the reading to submissions. Logs may be delayed or missing; attribute only traceable differences to specific people. Even if the last modifier is the user, that does not prove all draft changes are theirs; unsaved browser edits are not visible.

Show this change's before/after, any extra unpublished content and how it takes effect. For long body text and new questions show the actual content; never just say "there are other changes".

## Save and publish scope

| Situation | Handling |
| --- | --- |
| No draft | Automatically create a shared draft from the live version using the latest edit token; saving is not publishing |
| No other unpublished changes | Edit under the existing save authorization; if this batch has publish authorization, continue after verification, otherwise stop at draft |
| Other changes exist, no conflict with this change's fields | You may merge this change; before publishing the whole version, explain and obtain publish authorization for the extra content |
| This change's fields already have different draft values | Show the live value, the draft value and the user's target value; unless overwriting is explicitly chosen, decide first which to keep |
| Revision/token conflict on save or publish | Re-read and compare, and confirm only newly appeared business differences; if the scope is unchanged, continue with the new token; stop on repeated conflicts — never retry indefinitely |

Partial publishing is not supported. For example, if a colleague added two fields and the user only authorized a title change, you can save the title but must not publish the fields with it; let the user decide whether to keep the draft or authorize publishing the whole version. Never temporarily delete questions and add them back after publishing to simulate partial publishing.

## Minimal edits and read-back

- Call `update_form_draft` with the latest `form.edit_token` as `expected_edit_token`; omit `expected_revision_no` when there is no draft, and when there is a draft you may also pass the latest revision. Pass only this change's `title/subtitle/description/config`; editing questions or the activity still requires the complete corresponding payload; never send a read-back object back as is. See [form-definition.md](form-definition.md) for the exact contract.
- Build config at the actual merge granularity; omission means no change; `null` and empty values follow the field contract; arrays are not merged by index. Keep entries the user did not ask to delete.
- Read back after every write and check the target values and the related original values — e.g. after changing the header image height, the original image, position and visibility are still there. If anything is missing or inconsistent, stop publishing and report the actual status.
- To replace the image, read [header-image.md](header-image.md); when source text edits affect translations, read [translation.md](translation.md) — translation applies only to the latest published version, so draft edits must be published before they enter translation.
- When going live is needed, read [publish.md](publish.md); use the whole batch of confirmed differences as the boundary and do not create a new preview again. When an update fails or the result is unclear, read the status first and continue on the same form; never create a duplicate activity.
