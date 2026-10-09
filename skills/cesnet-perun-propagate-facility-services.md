---
generated: '2026-10-09'
method: generated
name: cesnet-perun-propagate-facility-services
title: Push service configuration to a facility
description: Inspect the services assigned to a facility, plan propagation of a service, and watch the facility state.
api: openapi/cesnet-perun-rpc-openapi.yml
operations:
- getFacilityByName
- getAssignedServices
- planServicePropagation
- getFacilityState
source: Grounded in openapi/cesnet-perun-rpc-openapi.yml (source openapi/_original/cesnet-perun-rpc-openapi.yml, OpenAPI 3.0.3).
  Every operationId verified verbatim in the spec.
---

# Push service configuration to a facility

Inspect the services assigned to a facility, plan propagation of a service, and watch the facility state.

## Auth
- Perun is self-hosted: the base URL is `https://{server}/{authentication}/rpc` (spec default `api-dev.perun-aai.org`). The `{authentication}` segment picks the method: `ba` (HTTP Basic), `oauth` (OAuth 2.0 bearer), `krb`, `cert`, `fed`, `non`.
- Send credentials matching that segment (securitySchemes `BasicAuth` / `BearerAuth`). Everything you can do is limited by your Perun roles; check them first with `getPerunPrincipal`.

## Steps
1. **getFacilityByName** (`GET /json/facilitiesManager/getFacilityByName`) — Resolve the facility by name.
2. **getAssignedServices** (`GET /json/servicesManager/getAssignedServices/f`) — List the services assigned to the facility; pick the service id.
3. **planServicePropagation** (`POST /urlinjsonout/servicesManager/planServicePropagation`) — Plan propagation of that service to the facility.
4. **getFacilityState** (`GET /json/tasksManager/getFacilityState`) — Poll the facility's propagation state until tasks finish or report an error.

## Rules
- `/json/...` operations take a JSON body or query parameters as defined in the spec; `/urlinjsonout/...` operations take query parameters and return JSON.
- Perun returns errors as a `PerunException` JSON object (`errorId`, `name`, `message`); read `name`/`message` before retrying.
- Operations flagged `x-agent-destructive` in `overlays/cesnet-perun-rpc-overlay.yaml` (delete*/remove*) need explicit user confirmation. Perun publishes no idempotency keys, so do not blindly retry create/add calls: look the object up first.
