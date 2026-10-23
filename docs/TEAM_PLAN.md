# Team plan: using this repo as the base

Snapshot analysed: `crowd-flow-intelligence-main`. Verified by running it: **backend 91/91 tests pass, frontend `tsc` + `vite build` pass**. Not verified: Docker build/run, any physical camera, real-browser playback, accuracy on real crowds (the repo's own docs say the same).

## What this repo is
A crowd **simulation + decision-support** platform (graph-based venue twin, deterministic risk engine, WebSocket dashboard) with camera registration and a browser-side people counter bolted on. It is *not yet* the blueprint/camera-sync system we planned.

## Already done (reuse)
| Area | State |
|---|---|
| Venue graph, simulation engine, risk thresholds | Working, tested (hard-coded demo venue) |
| Camera registration RTSP/ONVIF/USB | CRUD, Fernet-encrypted credentials, audit log, SSRF guard |
| Live video | MediaMTX gateway with signed expiring HLS tokens (verified with real MediaMTX + ffmpeg) |
| Live people count | Browser CenterNet -> integer count -> `/api/live/observations`, STALE handling |
| Security | Shared operator key, rate limits, CORS, WebSocket caps |
| History | Snapshot persistence + pruning (SQLite) |
| CI | GitHub Actions: pytest, tsc, build |

## Not done (the real gap vs our plan)
| Needed | Today |
|---|---|
| Multiple venues / floors | One hard-coded venue (`build_demo_venue`) |
| Blueprint upload, scale calibration, map editor | None |
| Map versioning (draft/approve/publish) | None |
| Camera position, heading, coverage on a map | Camera only has a `zone_id` string |
| Homography / video-to-map projection | None; detections carry no position |
| Always-on edge agent per floor | None; counting runs in a browser tab, USB is a stub (`agent_offline`) |
| ONVIF discovery, PTZ, snapshot | Gated off (reachability check only) |
| DB migrations | Alembic listed, no migrations |
| Per-user accounts / RBAC | One shared key |
| Background camera health polling | On-demand "Test connection" only |
| Real-camera + Docker verification | Not run |
| Auto blueprint -> map, cross-camera tracking | Out of scope for V1 |

## Code-health notes for the team
- `engine.py`, `graph/*.py`, `main.py` use very dense one-line style: hard to review. Reformat (ruff/black) in the first commit, before anyone branches.
- Two parallel node/edge models (`models.py` vs `graph/`) bridged by `graph/bridge.py`; their `NodeKind` values differ.
- `App.tsx` is a single 200-line component; no router.
- `docs/DEVELOPMENT_LOG.md` mentions uncommitted server-side YOLOX code under `backend/app/vision`. It is **not in this zip**; ask whoever has it.
- Coordinates are abstract 0-100 units; the plan needs metres.

## Phase 0 (one person, before anyone branches)
1. `git init`, push, protect `main`, require CI.
2. Run ruff/black on `backend`, prettier on `frontend` in one formatting-only commit.
3. Review and freeze `/contracts` (coordinates, venue, camera, event schemas).
4. Everyone clones and runs `pytest`, `npm run build` once.

## Tracks (see `/tracks/*/BRIEF.md`)
| Track | Folder | Starting point |
|---|---|---|
| A Edge/Vision | `/edge` | New; replaces browser counting + USB stub |
| B Server | `/backend` | Existing, 91 tests |
| C Web | `/frontend` | Existing, small |
| D Spatial | `/spatial` | New pure-Python package + one React component |

Each person works in their own folder on their own branch, codes against `/contracts`, and mocks the other tracks.

## Merge order
D (library) -> B (schema/API) -> A (agent sends real events) -> C (replace mocks with the real API). Integrate on a shared `integration` branch weekly; `pytest` and the frontend build must stay green.

## Rules
- Edit only your folder. Contract changes go through the lead.
- New code has tests; READMEs state what works and what does not.
- No secrets in Git (`.env` is ignored; keep it that way). Rotate any key that was ever committed.
- Do not claim a feature until a test or a run proves it.

## Suggested milestones
1. M1: video -> edge agent -> events -> server -> zone count on the dashboard (A + B).
2. M2: venue/floor/map CRUD + blueprint background + map editor (B + C).
3. M3: camera placement + homography calibration + positions on the map (D + A + C).
4. M4: alerts, history, per-floor deployment via Docker compose.
5. V2: auto blueprint proposals, multi-floor flow, cross-camera tracking.
