# Robotic Vision Pick System — Simulation

A browser-based interactive simulation of the stereo vision pick-and-place system described in the [Project Proposal](Robotic%20Vision%20Pick%20System%20-%20Project%20Proposal.docx). Built with Three.js. No server required — open `index.html` directly in a browser.

---

## What it demonstrates

- **Stereo camera rig** — two cameras at a calibrated physical baseline, rendered as independent WebGL viewports
- **Aperiodic IR structured light** — Poisson-disk dot pattern stamped into grayscale buffers before SAD matching, simulating an active IR emitter
- **Adaptive IR** — after each disparity pass, void regions are detected and supplemental IR dots are placed into those world positions, improving coverage on texture-less surfaces
- **SAD stereo matching** — Sum of Absolute Differences at full resolution with L-R consistency check, median filter, and color-guided void fill
- **Ground truth disparity** — per-pixel exact `d = f·B/Z` from a GPU depth pass; auto-ranged turbo colormap for comparison with computed disparity
- **U-Net training simulation** — simulates convergence of a learned stereo matching network by epoch-blending SAD output toward ground truth
- **6-DOF arm kinematics** — IK-solved pick-and-place arm with waypoint path planning and replan on obstruction
- **Pick cycle state machine** — IDLE → SCAN → APPROACH → DESCEND → PICK → LIFT → TRANSIT → PLACE → RETURN with live timing
- **Diagnostic mode** — arm traverses calibration poses; simulates Stage 1 + Stage 2 calibration health checks
- **Scene change tracking** — disparity is only recomputed when the scene changes, not every frame

---

## How to run

Open `index.html` in any modern browser (Chrome/Edge recommended for WebGL performance). No dependencies to install, no build step.

---

## Controls

| Control | Description |
|---|---|
| **Start Cycle** | Begin a pick cycle on the next available part |
| **Pause / Resume** | Freeze the simulation; cycle timer pauses accurately |
| **Stop** | Abort cycle and reset state |
| **Randomize** | Re-place all parts with no-overlap Poisson-disk placement |
| **Add Part** | Add one part at a safe random position |
| **Auto** | Continuously run pick cycles until platform is empty |
| **Run Diag** | Execute calibration diagnostic sequence |
| **Part size slider** | Scale all parts on the platform in real time |
| **Cam: RGB / IR / Combined** | Toggle each camera panel between raw RGB, IR dot pattern only, and combined overlay |
| **S / M / L** | Scale all UI text |
| Mouse drag | Orbit the 3D view |
| Scroll | Zoom |

---

## Camera & stereo parameters

| Parameter | Value | Notes |
|---|---|---|
| Baseline | 1.0 m physical (0.5 m per side) | Tuned to working distance |
| FOV | 52° vertical | C-mount equivalent |
| Focal length | ~246 px (at 320×240) | Derived from FOV |
| Depth range | 4.0 – 5.5 m | Matches platform depth in scene |
| Resolution | 320 × 240 | Per camera panel |

---

## Simulation vs. production

The simulation faithfully models the stereo vision pipeline geometry and matching logic. Some pipeline stages are approximated:

| Stage | Simulation | Production |
|---|---|---|
| Stereo matching | SAD + color fill | OpenCV SGBM / CUDA SGM |
| Object detection | Known scene positions | YOLOv8 inference |
| Pose estimation | Three.js transforms | ICP / FoundationPose |
| Motion execution | Animated IK | ROS2 MoveIt2 |
| Camera I/O | WebGL render | Spinnaker / Pylon SDK |

---

## Related

- **Project Proposal**: `Robotic Vision Pick System - Project Proposal.docx` — full system architecture, hardware BOM, software stack, diagnostics design, and market analysis.
