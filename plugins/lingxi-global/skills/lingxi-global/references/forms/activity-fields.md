# Activity Definition and Sessions

The current MCP provides activity fields, enums, conditions, defaults and examples; this skill does not maintain a second field table. For general request and update semantics, see [form-definition.md](form-definition.md).

## Fetch the contract on demand

Use `get_form_definition_options(section=activity)` to get the full activity contract; to query only the main settings or the time slots, use `activity_section=activity` or `slots`. A partial response may still depend on the main activity type and dates; do not treat it as a standalone save request.

- `payload_path` points to `definition.activity`, whose structure is `{activity:{...},slots:[...]}`, not expanded sessions. A full activity requires `ability=activity`.
- `fields` gives relative paths, types, whether required, nullability, enum candidates and validation rules; `*` denotes a list element.
- `conditions` distinguishes static required fields, business constraints applied at save time, normalization, and the conditions for generating valid sessions. `required=false` does not mean the field can be omitted in every mode. For example, a recurring activity with no selected dates may pass structural validation (preflight) yet produce no sessions.
- `defaults` are fallback values applied at save time, not choices the user has accepted. `examples` only illustrate structure; replace dates, time slots and locations with real business facts. Omitting some options enables an actual default (for example, check-in signature); check these against the business plan.

## Assembly and verification boundaries

The activity's registration period, quota, duplicate-registration setting and duplicate-detection rules are synced into the form config; do not submit two conflicting sets of values. Look up location area IDs level by level with `list_areas`; store the detailed address separately. Do not substitute text for an area, and do not pass off an indoor venue as online attendance. Saving a location does not prove the venue is booked.

For the main activity's common location, use `title/sort/location_remark` as described by the response's `service_extension_fields`. These fields come from the actual save service and are not in the formal validation rule table; do not treat them as additional validated constraints. On update, keep existing valid location data; do not copy every read-back system field verbatim.

A full activity update sends the complete activity plus `slots` and keeps existing time slot IDs; changing or deleting time slots is constrained by actual times and session status. When only a basic draft is required, you may omit the whole `activity`; never fill in fake dates to pass validation. After validation (preflight), still verify the actually saved dates, time slots and locations; passing the structure check does not mean the expected sessions were generated.

Do not implement the lifecycle by hand-writing `activity.status`. Follow [publish.md](publish.md) according to the current approval permission and authorization, and verify the live status after publishing. If the current MCP has no activity contract or cannot express what the user asks for, explain the capability gap; do not read PHP to piece together SaaS operations.
