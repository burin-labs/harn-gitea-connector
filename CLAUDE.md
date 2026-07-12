# CLAUDE.md - harn-gitea-connector

Pure-Harn connector package for self-hosted Gitea instances.

Shared Harn connector authoring rules live in the canonical guide:

- https://github.com/burin-labs/harn/blob/main/docs/src/connectors/authoring.md

Keep this file limited to provider-specific notes and local hazards. Add shared connector guidance
to the Harn guide first.

## Provider notes

- Webhook event names use `x-gitea-event`; delivery ids use `x-gitea-delivery`.
- Webhook signatures use `x-gitea-signature` HMAC when a signing secret is configured. The connector
  consumes `verify_hmac_signature` from `std/connectors/shared`, which accepts both
  `sha256=<hex>` and bare hex bodies.
- Reactive rate-limit handling reads `x-ratelimit-remaining` and `x-ratelimit-reset` (Unix seconds)
  on every API response. On a 429 or 503 with `remaining=0` and a reset within 60 seconds, the
  connector sleeps and retries once; resets further out return a `rate_limited` error.
- `rate_limit.acquire` exposes `rate_limit_token_bucket` from `std/connectors/shared` for
  proactive client-side throttling. State is keyed by `scope` and persisted in the connector
  store; tests inject `now_ms` for determinism.
- List endpoints (`pull_requests.list`, `issues.list`, `repositories.list`) take Gitea's
  `?page=&limit=` pagination. Their `*.list_all` variants walk pages via `paginate_cursor`,
  stopping when a partial page is returned or `max_pages` is reached.
- The default API base URL is a placeholder for local tests; real deployments should pass
  `api_base_url` and an access token or PAT, or set `GITEA_TOKEN`.
