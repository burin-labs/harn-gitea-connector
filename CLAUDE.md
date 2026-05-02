# CLAUDE.md - harn-gitea-connector

Pure-Harn connector package for self-hosted Gitea instances.

Shared Harn connector authoring rules live in the canonical guide:

- https://github.com/burin-labs/harn/blob/main/docs/src/connectors/authoring.md

Keep this file limited to provider-specific notes and local hazards. Add shared connector guidance
to the Harn guide first.

## Provider Notes

- Webhook event names use `x-gitea-event`; delivery ids use `x-gitea-delivery`.
- Webhook signatures use `x-gitea-signature` HMAC when a signing secret is configured.
- The default API base URL is a placeholder for local tests; real deployments should pass
  `api_base_url` and an access token or PAT, or set `GITEA_TOKEN`.
