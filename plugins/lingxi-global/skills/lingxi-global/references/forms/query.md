# Querying Forms and Activities

1. Use `list_forms` to find **published** forms and activities in the current organization that the current user can view. `keyword` fuzzy-matches titles; you can combine `is_activity`, `category_id`, `start_time_from`, `start_time_to`, `created_from`, `created_to`, `page`, `page_size`. Dates use the format `YYYY-MM-DD`. `start_time` is when the form opens for submissions, not when an activity session starts; when asked when an activity takes place, read the session data in the details; do not pass this filter off as the activity dates.
2. Show each candidate's title, type, open time, creation time and submission count, and keep the pagination total. Keep form IDs for your own follow-up calls; show them only if the user asks. When titles are similar, let the user choose; when the user gives only a public link, you may match it against the returned `client_url`; never guess the internal numeric ID from the encrypted ID.
3. Use `get_form(form_id)` to view the published definition, questions and links. It does not query unpublished drafts; when the draft ID is known and the user wants to view or edit the draft, go to the [edit-and-publish workflow](edit-and-publish.md) and read it with `get_form_draft`. If nothing matches or permission is insufficient, say so truthfully.

When verifying the live config as an admin before editing or publishing, use `get_form(form_id,include_config=true)`; extra config and activity details are whatever the actual response contains; do not infer that a config is empty because a field is missing from the default query. To check the form definition change history, use `get_form_changed_logs`; it returns only definition changes; query it with `page/page_size` and the returned pagination. Logs help with attribution; they do not replace comparing the actual live and draft content or checking the edit token.

## What the user sees

- Lead with the answer: 「共找到 26 份已發佈的表單與活動，以下是最近 5 份：」
- Then a table with only useful columns: title, type (表單 / 活動), open period, submissions, status. No IDs.
- End with what is left and what they can do: 「還有 21 份。要查看下一頁、按關鍵字篩選，或查看某一份的詳情嗎？」
- For one form's details, group by topic (basic info, questions, schedule, link) and show only what answers the question; give the public form link when the user wants to share it.
