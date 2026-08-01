---
name: palenca-verify-worker-income
description: Verify a Latin American worker's income and employment by creating a Palenca user, launching a Widget login, and reading back the connected account's profile, employment history, and earnings.
api: Palenca API
generated: '2026-07-20'
method: generated
source: openapi/palenca-vech-openapi.json + https://developers.palenca.com/docs/getting-started-1
operations:
- create_user_v1_users_post
- get_widget_v1_widgets__widget_id__get
- retrieve_user_accounts_v1_users__user_id__accounts_get
- retrieve_account_profile_v1_accounts__account_id__profile_get
- retrieve_account_employment_mexico_v1_accounts__account_id__employment_get-1
- retrieve_account_earnings_v1_accounts__account_id__earnings_search_post
---

# Verify a worker's income with Palenca

Base URL `https://api.palenca.com` (sandbox `https://sandbox.palenca.com`). Every request
sends `x-api-key: <your key>` and `Content-Type: application/json`. Global rate limit is
120 requests / 60 seconds — watch the `X-RateLimit-Remaining` header. Keep the
`X-Request-Id` from responses for support.

## Steps

1. **Create the user** — `POST /v1/users` (`create_user_v1_users_post`) with a `widget_id`
   and optional `external_id`. Returns a `user_id`.
2. **Launch the Widget** — retrieve the widget with `GET /v1/widgets/{widget_id}`
   (`get_widget_v1_widgets__widget_id__get`) and open the Widget (public link / CDN-ES6 /
   mobile) so the worker connects a gig platform (Uber, Rappi, DiDi, inDriver) or formal
   source (IMSS, CDT).
3. **Wait for the login result** — subscribe to webhooks (see the manage-webhooks skill)
   and act on `login.success` / `login.incomplete` / `login.error`. In sandbox, use magic
   identifiers like `uber+success@palenca-tests.com`; sandbox webhooks arrive after ~30s.
4. **List the accounts** — `GET /v1/users/{user_id}/accounts`
   (`retrieve_user_accounts_v1_users__user_id__accounts_get`) to get each `account_id`.
5. **Read profile + employment** — `GET /v1/accounts/{account_id}/profile`
   (`retrieve_account_profile_v1_accounts__account_id__profile_get`) and
   `GET /v1/accounts/{account_id}/employment`
   (`retrieve_account_employment_mexico_v1_accounts__account_id__employment_get-1`).
6. **Read earnings** — `POST /v1/accounts/{account_id}/earnings/search`
   (`retrieve_account_earnings_v1_accounts__account_id__earnings_search_post`) with a
   `start_date`/`end_date` and `options` (`page`, `items_per_page`); page until done.

## Error handling

Errors carry a machine-readable `error_code` (not RFC 9457). Handle `invalid_credentials`
(401), `platform_unavailable` (503), `request_timeout` (504), `too_many_requests` (429 —
retry next day for IMSS/ISSSTE), and `recent_login_successful` (412). Account-level
`warnings` (`rider_account`, `fleet_account`, `id_mismatch`) can accompany a success.
