---
generated: '2026-10-09'
method: generated
name: cesnet-perun-add-user-to-group
title: Add an existing user to a VO group
description: Resolve a VO, a user and a group by name, find the user's VO membership and add that member to the group.
api: openapi/cesnet-perun-rpc-openapi.yml
operations:
- getVoByShortName
- findRichUsers
- getMemberByUser
- getGroupByName
- addMember
- getGroupMembers
source: Grounded in openapi/cesnet-perun-rpc-openapi.yml (source openapi/_original/cesnet-perun-rpc-openapi.yml, OpenAPI 3.0.3).
  Every operationId verified verbatim in the spec.
---

# Add an existing user to a VO group

Resolve a VO, a user and a group by name, find the user's VO membership and add that member to the group.

## Auth
- Perun is self-hosted: the base URL is `https://{server}/{authentication}/rpc` (spec default `api-dev.perun-aai.org`). The `{authentication}` segment picks the method: `ba` (HTTP Basic), `oauth` (OAuth 2.0 bearer), `krb`, `cert`, `fed`, `non`.
- Send credentials matching that segment (securitySchemes `BasicAuth` / `BearerAuth`). Everything you can do is limited by your Perun roles; check them first with `getPerunPrincipal`.

## Steps
1. **getVoByShortName** (`GET /json/vosManager/getVoByShortName`) — Resolve the VO by its short name and capture its id.
2. **findRichUsers** (`GET /json/usersManager/findRichUsers`) — Search for the user by name, login or email; capture the user id.
3. **getMemberByUser** (`GET /json/membersManager/getMemberByUser`) — Get the member object linking that user to the VO (fails if the user is not a VO member).
4. **getGroupByName** (`GET /json/groupsManager/getGroupByName`) — Resolve the target group inside the VO by its name.
5. **addMember** (`POST /urlinjsonout/groupsManager/addMember`) — Add the member to the group.
6. **getGroupMembers** (`GET /json/groupsManager/getGroupMembers`) — Confirm the member now appears in the group.

## Rules
- `/json/...` operations take a JSON body or query parameters as defined in the spec; `/urlinjsonout/...` operations take query parameters and return JSON.
- Perun returns errors as a `PerunException` JSON object (`errorId`, `name`, `message`); read `name`/`message` before retrying.
- Operations flagged `x-agent-destructive` in `overlays/cesnet-perun-rpc-overlay.yaml` (delete*/remove*) need explicit user confirmation. Perun publishes no idempotency keys, so do not blindly retry create/add calls: look the object up first.
