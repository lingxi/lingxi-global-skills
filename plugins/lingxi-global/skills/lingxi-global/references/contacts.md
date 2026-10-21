# Contacts

Find contacts in the user's organization, tell similar ones apart, and read details. Read-only: this flow never creates, edits or exports contacts. Use only `list_contacts` and `get_contact`. If the tools are missing, authorization fails, or a contact is not visible to this organization, explain it and stop — never fall back to other APIs, the admin console's private endpoints, scripts or databases.

## 1. Search

- Use the clues the user gives: name, phone, email. If there is not enough to search on, ask first; do not pull the whole organization's contact list by default.
- `keyword` matches name, mobile, email, WhatsApp and landline. `name` is a fuzzy match; `mobile`, `email` and `whatsapp` are exact matches.
- You can combine `category_id`, `tag_id`, `created_from` and `created_to` (dates as `YYYY-MM-DD`).
- Different filter parameters are combined with AND. Only `keyword` matches any of the main fields (OR). When the user wants "name **or** phone", run separate searches, merge and de-duplicate by contact ID, and keep any other filters the user gave.
- Page through results with `page` / `page_size`; never treat the first page as everything.

## 2. Tell similar contacts apart

- Show the total and only what is needed to tell them apart, for example: name, masked phone (`9123 ****`), masked email, created date. Do not show contact IDs unless the user asks.
- Never pick the first result silently. Continue when there is exactly one clear match, or when the user chooses one.
- If the user already gave a specific contact ID, you may open it directly.

## 3. Details

- Call `get_contact(contact_id)` and treat its response as the source of truth.
- Show only the fields that answer the user's question, grouped by topic (contact methods, personal details, categories and tags).
- Identity documents, addresses and other sensitive data: show only when necessary and when the user is entitled to see them.
- If nothing is found or permission is missing, say so plainly. Being able to search does not mean the user can edit or bulk-export contacts.

## How results look

> 找到 3 位名叫「陳小明」的聯絡人：
>
> | 姓名 | 手提電話 | 電郵 | 建立日期 |
> | --- | --- | --- | --- |
> | 陳小明 | 9123 **** | chan***@example.com | 2026-08-12 |
> | … | … | … | … |
>
> 你要查看哪一位的詳細資料？
