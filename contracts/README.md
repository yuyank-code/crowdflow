# Contracts (frozen interfaces between tracks)

**Rule:** only the team lead edits this folder. If your track needs a change, open an issue/PR titled `contract: ...` and wait for approval. All four tracks code against these files and mock the others.

## Coordinate convention (decide once, never mix)
- **Map space** = metres on one floor. Origin = top-left of the blueprint, +x right, +y down.
- **Blueprint pixels** are only for drawing. Store `scale_m_per_px` + `origin_px` per floor and convert at the edges.
- **Camera pixels** (u, v) = pixel in the camera frame, origin top-left.
- The current demo venue uses abstract 0-100 units (see `backend/app/engine.py: build_demo_venue`). Track B migrates it to metres.
- Time: UTC ISO-8601 strings, produced by the edge unit's NTP-synced clock.

## Files
| File | Meaning | Main producer -> consumer |
|---|---|---|
| `venue.schema.json` | Venue, floor, zone, node, edge, map version | B/C -> everyone |
| `camera.schema.json` | Camera registration, placement, calibration status | B/C -> A, D |
| `events.schema.json` | Edge -> server messages (observation, camera_status) | A -> B |

## Compatibility
`POST /api/live/observations` (zone_id, camera_id, people, view_area_m2) already exists and keeps working as **v0**. The edge agent should use the `camera_observation` event (v1) once Track B adds the endpoint; B keeps v0 until the web client stops using it.

## Conventions added in v1 review
- Every schema carries `schema_version` (currently 1). Breaking changes bump it and the git tag (`contracts-v2`).
- Homography direction: **camera pixel (u, v) -> map metres (x, y)**.
- `heading_deg`: 0 = +x axis, clockwise as seen on screen (because +y points down).
- Edge events carry `event_id` (UUID) and a per-unit `seq`. Units buffer offline and may resend, so the server must treat `event_id` as idempotent.
- Stairs/lifts between floors are `floor_links` on the venue, not edges inside one floor's map.
