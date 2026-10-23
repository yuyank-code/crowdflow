# Track A - Edge / Vision (folder: `/edge`, new)

**Goal:** a Python agent that runs on the per-floor hardware, reads that floor's cameras, counts/locates people and sends events to the server.

## Why this track exists
Today people counting runs in the operator's browser tab (`frontend/src/live/detector.ts`, CenterNet via `@vladmandic/human`), only an integer count is sent, USB is a stub (`backend/app/cctv/adapters/usb.py` -> `agent_offline`), and counting stops when the tab closes. A real deployment needs an always-on agent.

## Tasks
1. Camera ingestion: RTSP + USB (OpenCV/FFmpeg), reconnect with backoff, last-frame watchdog, sub-stream support.
2. Person detector (YOLO-family, ONNX) behind an interface so the model can be swapped; detect every N frames.
3. Zone counting from camera_id -> zone_id mapping fetched from the server.
4. Emit `camera_observation` and `camera_status` events (see `/contracts/events.schema.json`).
5. Local queue (SQLite) for offline buffering; resend on reconnect.
6. Once Track D delivers the homography module: attach `positions_m` for calibrated cameras.
7. `Dockerfile` + `docker compose` service.

## Mock everything else
Use a looping video file or fake RTSP (MediaMTX + ffmpeg, see `scripts/test-gateway-e2e.sh`) and a tiny HTTP receiver instead of the real server.

## Do not touch
`/contracts`, `/backend`, `/frontend`, `/spatial` (ask the owner).

## Done when
A recorded video produces schema-valid events for 10 minutes, survives a killed network (events buffered, then delivered), and has unit tests for reconnect and queue logic. Document accuracy limits you measured; do not claim numbers you did not measure.
