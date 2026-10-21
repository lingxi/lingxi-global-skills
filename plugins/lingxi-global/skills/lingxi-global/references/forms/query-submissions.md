# Registration Status and Past Submissions

1. First determine the form's numeric `form_id`; if you only have a name or link, find and disambiguate it per [query.md](query.md). Confirm that `list_form_submissions` and `get_form_submission` are visible in the current session. Both read submissions of forms the current user has view permission on, and may contain personal data.
2. Use `list_form_submissions(form_id, ...)` to see the total, per-status counts and paginated summaries. You can filter by `status`, `source`, `created_from`, `created_to`. `summary` is the status distribution of all non-cart submissions of the form; `pagination.total` is the total for the current filter. Do not describe `pending_approval`, `pending_payment` or failed statuses as completed registration. The list does not contain full answers; when you need more results, page through by `pagination`; do not infer the full set from the first page.
3. Once the user specifies a submission, use `get_form_submission(form_id,submission_id)` to read the questions, answers and approval information as of the form version at submission time; show only the fields needed to answer the current question. Cart-held submissions are outside the scope of past submissions; stop if the submission is not found, belongs to another organization, or the user has no view permission on the form. `get_form_submission_result(form_id,request_id)` is only for checking the result of a submission this MCP user just made; it does not replace querying past submissions.
4. This workflow provides read-only registration status only; do not approve, modify or export submissions, or perform payment operations. The statistics are counts of submission statuses; they do not equal actual attendance or final payment amounts; those questions require verification through the corresponding business capability.

## What the user sees

- Lead with the status breakdown, e.g. 「共 6 份填報記錄：提交成功 4 份、付款失敗 2 份。」 Never count pending approval, pending payment or failed records as completed registrations.
- Then the most recent records in a table: submitted time, registrant name, status. Mask phone numbers and emails unless the user asks for them.
- Offer next steps: show more, filter by status or date, or open one record.
- For one record, show the answers grouped by question in plain language; omit system fields (device, IP, duration) unless the user asks.
