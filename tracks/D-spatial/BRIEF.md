# Track D - Spatial / Calibration (folder: `/spatial`, new Python package)

**Goal:** the geometry that makes "camera synced to map" real. Pure functions with tests; used by A (projection) and B (validation); UI part handed to C.

## Tasks
1. Homography: pixel (u, v) -> map (x, y) in metres via `cv2.findHomography` (RANSAC). Require >= 6 point pairs, report reprojection error in metres on **held-out** points.
2. Lens undistortion helper (checkerboard calibration). Fisheye/ceiling cameras are a separate, later pipeline.
3. Scale calibration: blueprint px <-> metres from a known-length line.
4. Graph validator: no isolated nodes, every zone linked to a node, an exit reachable from every node, edges reference existing nodes.
5. Coverage polygon derived from the calibrated image region projected onto the map.
6. Drift check: compare reference markers against the stored calibration, flag `needs_recalibration`.
7. Calibration UI component (React) for Track C.
8. Later (V2): blueprint -> wall-line proposals (OpenCV Hough), DXF/vector PDF parsing. These are suggestions the user must approve.

## Limits to document
Homography is valid only for a flat floor and a fixed, undistorted camera; stairs/slopes/zoom changes break it.

## Mock
Synthetic test data: render a known floor grid, project it with a known matrix, check the recovered matrix.

## Done when
Recovered homography error is below a threshold you state in the tests, and the validator rejects each broken-map case.
