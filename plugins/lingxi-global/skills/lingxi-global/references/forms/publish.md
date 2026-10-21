# Submit for approval, publish the whole draft, and verify go-live

A new form must first complete the full save and read back in the [creation workflow](create-and-publish.md); for an existing form, compare the live version with the shared draft per the [edit workflow](edit-and-publish.md). Publishing includes the entire draft, so every unpublished difference must be within the authorized scope.

## Current state and authorization

Use `get_form_draft` to read `form.draft_revision_no/edit_token`, `form.permission.can_manage`, and `audit_next_action/can_submit_audit/can_publish` in `form.audit`. The revision number does not increment on every save; every submit for approval or publish carries the latest `expected_revision_no` and `expected_edit_token`.

When the user has explicitly authorized the whole batch of a new or edited form to go live and the full read back matches, reuse that batch authorization; do not show the full content again after saving or ask for a separate go-live confirmation. When only the draft is authorized, the approval-submission notifications are not covered, or new business impact appears, confirm only the missing scope. `audit_required`, admin status or tool visibility never substitutes for permission under the current workflow.

| Current state | Action |
| --- | --- |
| `publish`, can manage, `can_publish=1`, draft revision > 0 | After getting publish authorization, call `publish_form_draft`; do not submit for approval again |
| `submit_audit`, can manage, `can_submit_audit=1` | Check that the existing authorization covers submitting for approval, the actual notification impact, and continuing to publish this batch once approved; if anything is missing, confirm only that scope. Re-read immediately after calling `submit_form_draft_audit`. Continue only while the same checked content is still within authorization and the state has become publishable; no need to ask again for each tool |
| `wait_audit`, `none` or insufficient permission | Deliver the waiting or blocked status; do not approve on the approver's behalf, and do not request a publish confirmation that cannot be executed now |

Approval is not publication. When resuming after a wait, or when the user has acted in the organization admin console, re-read permissions, the draft and the live state; if it is already published and this batch's content matches, verify the result and do not start it again. Handle edit token conflicts by comparing again per the edit workflow; new impact cannot be published along with the batch without authorization; do not retry endlessly. Unless there is separately authorized automation, do not promise to monitor or publish automatically after the session ends.

## Publish result and delivery

The publish response must be `status=published`; then use `get_form(form_id,include_config=true)` to verify the live revision, title, questions, relevant config and the actual `client_url`; when this change involves the header image, verify its reference, height, position and display state.

- Deliver the real public form link only when the read back matches and the link is non-empty; never assemble a domain yourself. If the form is published but the link is empty, explain that the client primary domain is missing; if the read back fails or does not match, distinguish the publish response from content not yet verified.
- Matching image/config data proves only the read back; say the corresponding display is verified only when you have evidence of actual page or resource access.
- If submit for approval or publish is rejected, keep the draft and explain the reason; when business content needs correcting, check the changes and authorization before continuing. On a timeout or unclear result, read the current state first; do not blindly replay the publish.
- A new form that is not yet published has no public form link; in that case you can only deliver the admin editor link (`edit_url`) from `get_form_draft` for the user themselves to review in the organization admin console; never call it a form link or a shareable preview. When a published form has a saved draft or is waiting for approval, the original public version and link usually stay valid; use the live read back to explain the old version versus the changes from this round that are not live yet.

Delivery only needs to state the actual status, the verified result or link, remaining blockers and the next step; do not treat optional follow-up improvements as meaning creation is unfinished. In user-facing replies, do not expose internal field names, tool names, JSON or IDs unless the user asks.
