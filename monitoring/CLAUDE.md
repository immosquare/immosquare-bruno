# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What is this repo

Bruno API collection for monitoring Immosquare's infrastructure. Uses [Bruno](https://www.usebruno.com/) (open-source API client, alternative to Postman). All requests are stored as `.bru` files in a folder-based structure.

## Architecture

### Environments

Two environments in `environments/`:
- **development** — `baseurl: immosquare.me:4005`, includes `ubiflow_password` secret
- **production** — `baseurl: monitoring.immosquare.com`

Secret variables (managed in Bruno UI, never committed): `bearer_token`, `bearer_token_emails`, `bearer_token_slack`, `bearer_token_gateways`.

### Request collections

| Folder       | Purpose                                                        | Auth                    |
|-------------|----------------------------------------------------------------|-------------------------|
| `internal/` | Health checks for all apps (EU, CA, Local) via `POST /api/internal/health` | Bearer (`bearer_token`) |
| `emails/`   | Send emails via `POST /api/emails/send`                        | Bearer (`bearer_token_emails`) |
| `slack/`    | Send Slack notifications via `POST /api/slack/notify`          | Bearer (`bearer_token_slack`) |
| `gateways/` | Gateway sync via `POST /api/gateways/:id/sync`                | Bearer (`bearer_token_gateways`) |
| `dns/`      | DNS zone record management (create/get)                        | Static JWT in headers   |
| `v1/`       | Centris broker APIs                                            | None                    |
| `ubiflow/`  | Ubiflow auth + listing retrieval (post-response script saves token) | None / Bearer from login |

### Monitored apps

Health checks cover ~12 apps across staging/preprod/production: agconnect, appgenerator, cloudphoto, mlsconnect, monitoring, myprint, prestigemls, shareimmo, socialimmo, storeimmo.

## Working with .bru files

- `.bru` is Bruno's markup format — structured blocks (`meta`, `get`/`post`, `headers`, `auth:bearer`, `body:json`, `script:post-response`, etc.)
- `folder.bru` files define folder-level metadata and inherited auth
- `{{variable}}` syntax references environment variables
- `vars:secret` in environment files = values stored locally, never in the file
