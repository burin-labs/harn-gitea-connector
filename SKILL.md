# SKILL: harn-gitea-connector

Use `harn-gitea-connector` when wiring Harn triggers or outbound helpers for Gitea.

## What you get

- Provider id: `gitea`
- Trigger kinds: `webhook`
- Supported events: `push`, `pull_request`, `issues`, `issue_comment`,
  `release`, `repository`, and `star`
- Webhook verification: `gitea_hmac`
- Outbound helpers: `api.request`, `pull_requests.comment`,
  `pull_requests.update`, `issues.comment`, `commit_status.set`, and
  `repository_file.get`

## Trigger recipe

```toml
[[triggers]]
id = "gitea-events"
kind = "webhook"
provider = "gitea"
match = { path = "/hooks/gitea", events = ["pull_request.opened"] }
handler = "handlers::on_gitea_event"
secrets = { signing_secret = "gitea/webhook-secret" }
```

Every webhook request must carry a binding id and a valid HMAC signature.

Validate the package with `harn connector test . --provider gitea`.
