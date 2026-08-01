---
name: palenca-manage-webhooks
description: Subscribe to and manage Palenca real-time webhooks so your backend reacts to login and account events (login.success, account.connected, earnings.updated) instead of polling.
api: Palenca API
generated: '2026-07-20'
method: generated
source: openapi/palenca-vech-openapi.json + https://developers.palenca.com/docs/webhooks
operations:
- create_webhook_v1_webhooks_post
- list_webhooks_v1_webhooks_get
- retrieve_webhook_v1_webhooks__webhook_id__get
- update_webhook_v1_webhooks__webhook_id__put
- delete_webhook_v1_webhooks__webhook_id__delete
---

# Manage Palenca webhooks

Data extraction takes 10–180 seconds per platform, so webhooks are strongly recommended
over polling. Auth is `x-api-key`; base URL `https://api.palenca.com`.

## Steps

1. **Create a webhook** — `POST /v1/webhooks` (`create_webhook_v1_webhooks_post`) with
   `url`, `name`, the associated `widget_id`/`connect_flow_id`, `is_sandbox`, and
   `sign_request: true` to receive signed requests.
2. **List / inspect** — `GET /v1/webhooks` (`list_webhooks_v1_webhooks_get`) and
   `GET /v1/webhooks/{webhook_id}` (`retrieve_webhook_v1_webhooks__webhook_id__get`).
3. **Update or delete** — `PUT /v1/webhooks/{webhook_id}`
   (`update_webhook_v1_webhooks__webhook_id__put`) /
   `DELETE /v1/webhooks/{webhook_id}` (`delete_webhook_v1_webhooks__webhook_id__delete`).
4. **Handle events** — your endpoint receives HTTP POST JSON for events including
   `login.created`, `login.success`, `login.incomplete`, `login.error`, `login.retry`,
   `account.connected`, `account.requires_auth`, `profile.updated`, `employment.updated`,
   `earnings.updated`, `events.updated`, and `clean_save.finished`. Respond 2xx quickly and
   process asynchronously.

## Testing

Use a sandbox webhook (`is_sandbox: true`) with a magic identifier login; notifications
arrive after a ~30-second delay. `webhook.site` is handy for quick inspection.
