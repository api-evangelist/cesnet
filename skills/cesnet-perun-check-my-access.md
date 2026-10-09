---
generated: '2026-10-09'
method: generated
name: cesnet-perun-check-my-access
title: Check my Perun identity and roles
description: Find out who the authenticated Perun principal is, which roles it holds, and which virtual organizations it can
  see or administer.
api: openapi/cesnet-perun-rpc-openapi.yml
operations:
- getPerunPrincipal
- getMyVos
- getVosWhereUserIsAdmin
source: Grounded in openapi/cesnet-perun-rpc-openapi.yml (source openapi/_original/cesnet-perun-rpc-openapi.yml, OpenAPI 3.0.3).
  Every operationId verified verbatim in the spec.
---

# Check my Perun identity and roles

Find out who the authenticated Perun principal is, which roles it holds, and which virtual organizations it can see or administer.

## Auth
- Perun is self-hosted: the base URL is `https://{server}/{authentication}/rpc` (spec default `api-dev.perun-aai.org`). The `{authentication}` segment picks the method: `ba` (HTTP Basic), `oauth` (OAuth 2.0 bearer), `krb`, `cert`, `fed`, `non`.
- Send credentials matching that segment (securitySchemes `BasicAuth` / `BearerAuth`). Everything you can do is limited by your Perun roles; check them first with `getPerunPrincipal`.

## Steps
1. **getPerunPrincipal** (`GET /json/authzResolver/getPerunPrincipal`) — Read the principal: user, roles and the auth method actually used.
2. **getMyVos** (`GET /json/vosManager/getVos`) — List the virtual organizations (VOs) visible to you.
3. **getVosWhereUserIsAdmin** (`GET /json/usersManager/getVosWhereUserIsAdmin`) — For your user id from step 1, list the VOs you administer.

## Rules
- `/json/...` operations take a JSON body or query parameters as defined in the spec; `/urlinjsonout/...` operations take query parameters and return JSON.
- Perun returns errors as a `PerunException` JSON object (`errorId`, `name`, `message`); read `name`/`message` before retrying.
- Operations flagged `x-agent-destructive` in `overlays/cesnet-perun-rpc-overlay.yaml` (delete*/remove*) need explicit user confirmation. Perun publishes no idempotency keys, so do not blindly retry create/add calls: look the object up first.
