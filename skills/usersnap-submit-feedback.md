---
name: Submit user feedback to a Usersnap project
description: Programmatically submit a feedback item (comment, rating, visitor email, attachments) to a Usersnap project via the platform REST API.
api: openapi/usersnap-api-openapi-original.json
operations: [getProjectAssignees, getProjectMetrics, submitFeedback]
generated: '2026-07-21'
method: generated
---

# Submit user feedback to a Usersnap project

## Auth
The REST API is plan-gated. Create an HS256 JWT signed with the shared JWT
secret from `https://app.usersnap.com/#/settings/rest-api`; the JWT header
must include `"kid": "<JWT ID>"` (never HS512). Send it on every request as
`Authorization: Bearer <jwt>`. See `authentication/usersnap-authentication.yml`.

Base URL: `https://platform.usersnap.com/v0.1`

## Steps
1. (Optional) `getProjectAssignees` — `GET /projects/{api_key}/assignees` to
   pick a `user_id` for the `assignee` field. If you omit `assignee`, the
   project's default assignee is used; pass `null` to leave it unassigned.
2. (Optional) `getProjectMetrics` — `GET /projects/{api_key}/metrics` to see
   which fields (Comment, Rating, Visitor, Custom Data, ...) the project accepts.
3. `submitFeedback` — `POST /projects/{api_key}/feedbacks` with a JSON body.
   All fields are optional: `visitor` (email), `comment` (text), `assignee`,
   `rating` (1-10 NPS, 1-5 stars/smileys, 1/-1 thumbs), `client`
   (`url`, `screen_width`, `screen_height`), `attachments[]`
   (`content` base64, `filename`, `mime_type`; max 20MiB each).
   A `201` returns `data.feedback.feedback_id`.

## Rules
- The `api_key` path parameter is the project API key (not the project_id).
- Errors come back as `{status: false, msg, errortype, errordata}` — see
  `errors/usersnap-problem-types.yml` (`WRONG_AUTH` = 401, `SITE_NOT_FOUND` = 400).
- There is no idempotency key; retry only when you can tolerate duplicates.
