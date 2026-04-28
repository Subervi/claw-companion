# Tooling

> Tools, services, and connectors used to build Claw Companion. This document is intentionally separate from `PROJECT_PLAN.md` and `ROADMAP.md` because tooling evolves faster than the product itself. Update this freely as new tools become available.

---

## Development

### Code editor / AI coding assistant
- **Claude Code** (Anthropic) — primary code generator and pair programmer. Used to write Python scripts, refactor, and explain code.
- **Cursor** or **VS Code** — fallback editor for manual edits and review.

### Version control
- **Git + GitHub** — public repo at `claw-companion`.
- **Branching**: `main` is always working. New phases/features as branches, merged when stable.

### Project management
- **GitHub Issues** — one issue per milestone (M0.1, M0.2, etc.)
- **GitHub Projects** (kanban) — optional, only if it helps. Don't over-engineer planning.

---

## Communication & Documentation

### Architecture & planning
- **Claude (chat)** — for high-level planning, decision-making, brainstorming, document writing.
- **Markdown** — all project docs (`PROJECT_PLAN.md`, `ROADMAP.md`, `TOOLING.md`, READMEs).

### Public journaling
- **X (Twitter)** — development journal. One post per major milestone, not per commit. Aim for quality over noise.

---

## Hardware Design (3D, CAD, Renders)

This section is the most important for Phase 3 (gimbal) and Phase 5 (final enclosure). Tools here let us design and visualize without becoming CAD experts.

### Mechanical design (CAD)
- **Autodesk Fusion 360** + **Claude Fusion connector** (MCP)
  - Released by Anthropic in late April 2026. Allows designing and modifying 3D models conversationally with Claude.
  - **Use for:** designing the gimbal bracket, the cylindrical body, mounting plates, the rotating head shell.
  - **How it helps:** describe what you want ("I need a bracket that holds two HS-5485HB servos in pan-tilt configuration with a 60mm display on top"), Claude builds it iteratively in Fusion.
  - **Why this is huge:** removes CAD learning curve as a project blocker. Was the most likely bottleneck to reaching Phase 5.
  - **Plan:** start using it actively in Phase 3 (mechanical mount for servos) and heavily in Phase 5 (final enclosure).

- **FreeCAD** — open source alternative if Fusion becomes a problem (license, paywall, etc.).

### Renders & marketing visuals
- **Blender** + **Claude Blender connector** (MCP)
  - Released same time as Fusion connector. Allows scene debugging, batch material changes, scripting via natural language.
  - **Use for:** photorealistic product renders (for X posts, README header, future website).
  - **Use for:** short product animations (head rotating, expressions changing) for promotion.
  - **Plan:** use selectively from Phase 1 onwards when we want a "good-looking" render to share. Heavy use in Phase 5 launch.

- **DALL-E / GPT-4o image generation** — for early concept art and idea exploration. Already used to generate the initial concept sketches.

### 3D printing
- **Bambu Lab printer** (X1C / P1S) or any FDM printer with PLA/PETG capability.
  - Print the enclosure parts in Phase 5.
  - PLA for prototypes, PETG for final (more heat-resistant, better for housing electronics).
- **Printables / Thingiverse** — for browsing existing companion robot designs as references (not to copy directly).

---

## Voice & AI Pipeline

### Speech-to-Text (STT)
- **faster-whisper** — primary local STT. Optimized version of OpenAI Whisper, runs well on PC.
- **OpenAI Whisper API** — fallback if local quality is insufficient.
- **Whisper.cpp** — alternative for running on Pi 5 if we ever want STT on-device.

### Text-to-Speech (TTS)
- **Piper TTS** — primary local TTS. Spanish voices available, runs fast on PC.
- **ElevenLabs API** — premium fallback for higher quality voice. Decide in Phase 0 if needed.

### Wake word detection
- **openWakeWord** — primary wake word detection. Open source, custom training supported.
- **Picovoice Porcupine** — fallback (free for personal use).

### Voice activity detection (VAD)
- **Silero VAD** — lightweight, accurate. Detects when user is speaking.

### Vision processing
- **Claude API** (Anthropic) — vision model for "what do you see?" queries. Already have API access.
- **GPT-4o Vision API** — alternative vision provider.
- **OpenCV** — local face detection for Phase 3 face tracking. No external API needed.

---

## Connectors / Integrations

### OpenClaw integration
- **OpenClaw Gateway** (`http://localhost:18789`) — official HTTP API for talking to OpenClaw from external clients.
- This is the only "official" integration we depend on.

### Hardware control libraries (Python)
- **sounddevice** — audio I/O.
- **pygame** — face animation rendering.
- **picamera2** — Pi camera control.
- **adafruit-pca9685** — servo controller via I2C.
- **gpiozero** — basic GPIO (LEDs, buttons).
- **smbus2** — I2C sensors (accelerometer, light sensor).
- **rpi_ws281x** — NeoPixel LED ring control.

---

## Backend (Bridge Server)

- **FastAPI** — Bridge Server framework. Lightweight, async, well-documented.
- **uvicorn** — ASGI server for FastAPI.
- **httpx** — HTTP client (used by both Pi and Bridge Server).
- **systemd** — running Bridge Server as a service on Linux PC.

---

## Operating Systems

- **Arch Linux** (development PC) — where OpenClaw runs and code is written.
- **Raspberry Pi OS 64-bit** (device) — primary OS for the Pi.
- **Arch Linux ARM** — possible alternative for the Pi if we want OS consistency.

---

## Sourcing / Hardware Suppliers

- **Pi5 + accessories**: Berrybase (DE), The Pi Hut (UK), Mouser (EU), local Spanish distributors.
- **ReSpeaker**: Seeed Studio (direct), Mouser, Amazon.
- **Servos (digital metal-gear)**: Hobbyking, Banggood, AliExpress (caveat: shipping times).
- **Sensors (MPU-6050, BH1750, NeoPixel)**: AliExpress, Adafruit, Pimoroni.
- **3D printing filament**: Bambu Lab (PLA Basic), Prusament (PETG).

---

## Tools by Phase (Quick Reference)

### Phase 0
Claude Code, Python, faster-whisper, Piper, pygame, OpenClaw Gateway, GitHub, X account.

### Phase 1
All of Phase 0 + Pi 5, Raspberry Pi OS, ReSpeaker libraries, openWakeWord, FastAPI, systemd.

### Phase 2
All of Phase 1 + picamera2, OpenCV, Claude/GPT-4o Vision API.

### Phase 3
All of Phase 2 + adafruit-pca9685, gpiozero, **Fusion 360 + Claude Fusion connector** (for servo bracket design).

### Phase 4
All of Phase 3 + smbus2, rpi_ws281x, custom-trained openWakeWord model.

### Phase 5
All of Phase 4 + **Fusion 360 + Claude Fusion connector** (heavily, for final enclosure), **Blender + Claude Blender connector** (renders, animations for launch), 3D printer.

---

## Decision Log: Why These Tools

- **Why Claude Code over Cursor/Copilot?** Already paying for Claude Pro. Better at multi-file refactors and architectural reasoning. Tighter integration with Anthropic's vision API later.
- **Why faster-whisper over Whisper API?** Privacy + zero per-query cost. Quality is sufficient for Spanish.
- **Why Piper over ElevenLabs?** Free, local, no internet dependency. ElevenLabs reserved for if/when we want premium voice quality.
- **Why Fusion 360 over FreeCAD or Blender for mechanical design?** Claude Fusion connector exists. FreeCAD has no AI assistance, would block progress for non-CAD users.
- **Why FastAPI over Flask?** Async-native, auto-generated OpenAPI docs, modern Python.
- **Why GitHub over GitLab?** Bigger community, better integrations (Claude Code, Codespaces), discoverability for open source.

---

## Costs Summary (Software / Services)

| Tool | Cost | Notes |
|---|---|---|
| Claude Pro | €18/month | Already paying. |
| Claude API | Pay-per-use | For OpenClaw + vision queries. ~€5-15/month estimated. |
| Anthropic MCP connectors (Fusion, Blender) | Included with Claude | No extra cost. |
| Fusion 360 | Free for personal use | Hobbyist license. |
| Blender | Free | Open source. |
| ElevenLabs (optional) | $5-22/month | Only if Piper insufficient. |
| GitHub | Free | Public repos. |
| X | Free | |
| **Estimated monthly software cost** | **~€20-40** | |

---

## What's NOT Used (and Why)

- **Home Assistant** — not the focus. OpenClaw can do home control via its own skills if needed. Adding HA mid-project would scope-creep.
- **ROS / ROS 2** — overkill for this project. We're not building autonomous navigation.
- **Docker** — adds complexity without clear benefit on this hardware. Direct Python on Pi is simpler.
- **Kubernetes** — absolutely not.
- **A custom mobile app** — Phase 6+ if ever. Don't waste effort here in v1.0.

---

## Update History

- **2026-04-28** — Initial document. Added Fusion 360 + Blender Claude connectors as critical Phase 5 tools.
