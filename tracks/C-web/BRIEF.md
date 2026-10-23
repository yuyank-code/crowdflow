# Track C - Frontend (folder: `/frontend`, existing)

**Goal:** a map editor and a live dashboard drawn on the venue floor plan.

## Starting point (verified: typecheck + build pass)
React 18 + Vite + TS. `App.tsx` (203 lines) is one component holding dashboard + simulation controls; `CameraManagement`, `CameraPreview` (HLS), `LiveZone` (browser counting) exist. No router, no state library, no map editor.

## Tasks
1. Split `App.tsx` into pages (Dashboard, Cameras, Map editor) and a small API/state layer.
2. Venue + floor selector.
3. Map editor (Konva or SVG): blueprint image as background, scale-calibration tool (draw a line of known length), draw zones (polygons), nodes, edges, place cameras with heading + coverage polygon, draft/approve/publish workflow.
4. Live view on the floor plan: zone occupancy colours, camera status markers, alerts list.
5. Host Track D's calibration component (click matching points on a video frame and the floor plan).
6. Code-split heavy bundles (build warns: `human.esm` ~1.6 MB, `hls` ~0.6 MB).
7. Keep browser counting only as an optional demo mode once Track A works.

## Mock
Generate a mock server from `/contracts` + OpenAPI (MSW or Prism) and fake WebSocket data until Track B is ready.

## Done when
Full flow works on the mock API: load blueprint -> set scale -> draw map -> place camera -> see live fake counts on it. Add component tests and one browser (Playwright) test.
