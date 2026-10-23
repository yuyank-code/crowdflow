# Track B - Backend / Server (folder: `/backend`, existing)

**Goal:** turn the single hard-coded demo venue into a real multi-venue, multi-floor, versioned system and ingest edge events.

## Starting point (verified: 91 tests pass)
Working: FastAPI app, simulation engine, risk thresholds, camera CRUD with encrypted credentials + audit log + SSRF guard, MediaMTX token gateway, live store, WebSocket, snapshot history.

## Tasks
1. DB models + **Alembic migrations** (Alembic is listed as a dependency but no migrations exist): venues, floors, map_versions (draft -> approved -> published), zones, nodes, edges, camera_placements, calibrations.
2. Replace `build_demo_venue()` in `app/engine.py` with venues loaded from the DB (keep the demo venue as seed data). Migrate coordinates to metres (see `/contracts/README.md`).
3. Unify the two parallel node/edge models (`app/models.py` vs `app/graph/*`, joined by `graph/bridge.py`); note `NodeKind` already differs between them.
4. CRUD APIs for venues/floors/maps/zones/nodes/edges and camera placement; publish OpenAPI.
5. Ingest endpoint for `camera_observation` / `camera_status` events from edge units (device-token auth). Keep `/api/live/observations` (v0) working.
6. Make live state per-venue and persist it (today `live/store.py` is in memory, one entry per zone).
7. Per-user accounts/RBAC replacing the single shared operator key; use the existing audit log pattern for all changes.
8. Graph validation endpoint using Track D's validator before a map can be approved.
9. PostgreSQL option for production.

## Mock
Edge: a script that POSTs fake events. Spatial: stub validator that returns ok.

## Done when
Every endpoint has tests, `pytest` stays green (currently 91), migrations run on a blank DB, and two venues with two floors each can coexist.
