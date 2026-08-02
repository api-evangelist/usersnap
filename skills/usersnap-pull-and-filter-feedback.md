---
name: Pull and filter feedback from Usersnap
description: List projects, page through feedback items with cursors, and run structured filter queries (state, rating, email domain, date range) against the Usersnap platform REST API.
api: openapi/usersnap-api-openapi-original.json
operations: [getProjects, getProjectFeedbacks, getProjectFeedbacksCount, filterProjectFeedbacks, filterProjectFeedbacksCount]
generated: '2026-07-21'
method: generated
---

# Pull and filter feedback from Usersnap

## Auth
`Authorization: Bearer <self-signed HS256 JWT with kid header>` — see
`authentication/usersnap-authentication.yml`. Base URL:
`https://platform.usersnap.com/v0.1`.

## Steps
1. `getProjects` — `GET /projects` to list projects available to the token;
   grab `project_id` (and `api_key` if you also submit).
2. `getProjectFeedbacks` — `GET /projects/{project_id}/feedbacks?limit=100`
   pages with cursors: `after` = last `feedback_id` of the page, `before` =
   first. Response carries `has_more`, `count`, and a ready-made `next`
   object (`query_string`, `limit`, `after`). Default sort: `updated_at` desc.
3. `getProjectFeedbacksCount` — `GET /projects/{project_id}/feedbacks/count`
   for the total.
4. `filterProjectFeedbacks` — `POST /projects/{project_id}/feedbacks/filter`
   with a JSON body (REQUIRED even if empty — send `{}`):
   - `query[]`: `{filter_type, operator, value}` — filter_type in
     [assignee, created_at, email, feedback_number, nps, one_to_five, state, thumb];
     operator in [eq, neq, in, not_in, gt, gte, lt, lte, like, ilike].
   - `order_by`: `{order_by_type: created_at|rating|updated_at|likes, direction: asc|desc}`.
   Example — done items, newest first:
   `{"order_by":{"direction":"desc","order_by_type":"created_at"},"query":[{"filter_type":"state","operator":"eq","value":"done"}]}`
5. `filterProjectFeedbacksCount` — `POST /projects/{project_id}/feedbacks/filter/count`
   for the filtered total (ordering only matters when pagination is passed).

## Rules
- Cursor params: `limit` 1-100 (default 10).
- Errors: `{status: false, msg, errortype, errordata}` envelope; 401 = `WRONG_AUTH`.
- Feedback items embed client context, screenshot/screen-recording URLs,
  `ordered_inputs[]`, and `labels[]` — see `data-model/usersnap-data-model.yml`.
