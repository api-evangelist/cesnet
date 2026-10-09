---
generated: '2026-10-09'
method: generated
name: cesnet-perun-grant-group-access-to-facility
title: Grant a group access to a facility resource
description: Give a group access to a service by assigning it to one of a facility's resources, then check the assignment.
api: openapi/cesnet-perun-rpc-openapi.yml
operations:
- getFacilityByName
- getAssignedResourcesForFacility
- getGroupByName
- assignGroupToResource
- getAssignedGroups
source: Grounded in openapi/cesnet-perun-rpc-openapi.yml (source openapi/_original/cesnet-perun-rpc-openapi.yml, OpenAPI 3.0.3).
  Every operationId verified verbatim in the spec.
---

# Grant a group access to a facility resource

Give a group access to a service by assigning it to one of a facility's resources, then check the assignment.

## Auth
- Perun is self-hosted: the base URL is `https://{server}/{authentication}/rpc` (spec default `api-dev.perun-aai.org`). The `{authentication}` segment picks the method: `ba` (HTTP Basic), `oauth` (OAuth 2.0 bearer), `krb`, `cert`, `fed`, `non`.
- Send credentials matching that segment (securitySchemes `BasicAuth` / `BearerAuth`). Everything you can do is limited by your Perun roles; check them first with `getPerunPrincipal`.

## Steps
1. **getFacilityByName** (`GET /json/facilitiesManager/getFacilityByName`) — Resolve the facility (the managed system) by name.
2. **getAssignedResourcesForFacility** (`GET /json/facilitiesManager/getAssignedResources`) — List the resources defined on that facility and pick the target resource.
3. **getGroupByName** (`GET /json/groupsManager/getGroupByName`) — Resolve the group to grant access to.
4. **assignGroupToResource** (`POST /urlinjsonout/resourcesManager/assignGroupToResource`) — Assign the group to the resource.
5. **getAssignedGroups** (`GET /json/resourcesManager/getAssignedGroups`) — Confirm the group is assigned to the resource.

## Rules
- `/json/...` operations take a JSON body or query parameters as defined in the spec; `/urlinjsonout/...` operations take query parameters and return JSON.
- Perun returns errors as a `PerunException` JSON object (`errorId`, `name`, `message`); read `name`/`message` before retrying.
- Operations flagged `x-agent-destructive` in `overlays/cesnet-perun-rpc-overlay.yaml` (delete*/remove*) need explicit user confirmation. Perun publishes no idempotency keys, so do not blindly retry create/add calls: look the object up first.
