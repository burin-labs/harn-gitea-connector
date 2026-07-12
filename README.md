# harn-gitea-connector

Pure-Harn Gitea connector: signed webhooks, self-hosted REST dispatch, and raw
API helpers.

This package implements the Harn Connector interface contract v1 for `gitea`.
It normalizes inbound webhook payloads to the tagged `NormalizeResult` envelope,
verifies provider-specific webhook signatures, and exposes outbound raw API helpers
plus common PR/comment/status method aliases.

## Install

```sh
harn add github.com/burin-labs/harn-gitea-connector@v0.1.0
```

Use a path checkout for unreleased `main` or local multi-repo development:

```toml
[dependencies]
harn-gitea-connector = { path = "../harn-gitea-connector" }
```

## Webhook verification

Gitea webhooks must be signed. Configure `signing_secret` or
`gitea/webhook-secret`; the connector verifies the `x-gitea-signature`
HMAC-SHA256 header against the raw request body and rejects requests with no
configured secret, missing binding id, missing signature, or invalid signature.

## Authentication

Outbound calls use an access token scoped to the target Gitea instance. Pass
`access_token`, `token`, `personal_access_token`, or `app_password` in the call
args, or set the `GITEA_TOKEN` environment variable.

## Development

```sh
harn check src/lib.harn
harn fmt --check src/lib.harn tests/*.harn
for test in tests/*.harn; do harn run "$test" || exit 1; done
```

## License

Dual-licensed under MIT and Apache-2.0.
