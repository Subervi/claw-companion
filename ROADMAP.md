# Roadmap

> Detailed phase planning for the Claw Companion project.

This document tracks what gets built, in what order, and what defines "done" for each phase. Each phase adds exactly one new capability on top of a fully working previous phase.

---

## Phase 0 — Proof of Concept on PC

**Duration:** 1–2 weeks
**Goal:** Validate the full voice loop with OpenClaw, no hardware purchased.
**What's new:** Everything (the foundation).

### Milestones

#### M0.1 — Repo and infrastructure setup
- [ ] GitHub repo `claw-companion` created (public)
- [ ] `PROJECT_PLAN.md` and `ROADMAP.md` committed
- [ ] X account created
- [ ] First X post: "Starting this project, here's the plan, will document everything"
- [ ] Confirm OpenClaw Gateway is reachable at `http://127.0.0.1:18789`

#### M0.2 — OpenClaw Gateway API investigation
- [ ] Document the Gateway endpoints (request/response format)
- [ ] Send a test message via curl, get a response
- [ ] Replicate the test in Python with `httpx`

#### M0.3 — Audio capture + STT
- [ ] Capture audio from PC mic with `sounddevice`
- [ ] Implement VAD (voice activity detection)
- [ ] Transcribe with `faster-whisper` (small model, multilingual)
- [ ] Validate Spanish transcription quality

#### M0.4 — TTS playback
- [ ] Install Piper TTS with Spanish voice
- [ ] Synthesize a test phrase, play it back
- [ ] Validate Spanish voice quality (decide if Piper is enough or need ElevenLabs)

#### M0.5 — Animated face (basic)
- [ ] Pygame window with horizontal layout
- [ ] 5 base states: `idle`, `listening`, `thinking`, `speaking`, `error`
- [ ] State transitions triggered by Python events
- [ ] Inspired by RoboEyes / Tabbie style

#### M0.6 — Full integration
- [ ] Loop: capture → STT → OpenClaw → TTS → playback, all synchronized with face state
- [ ] Latency measured and documented
- [ ] Demo video recorded
- [ ] Second X post: "Phase 0 working, here's the demo"

### Phase 0 Done Criteria
- Speak to the PC, OpenClaw responds, the face reacts appropriately, response is played back as audio.
- Total round-trip latency under 5 seconds for typical queries.
- Documentation in repo lets someone else reproduce the setup.

---

## Phase 1 — Hardware Migration: Voice + Display

**Duration:** 3–5 weeks after Phase 0
**Goal:** Run the Phase 0 experience on dedicated hardware. Static device, no movement, no camera.
**What's new:** Pi 5, ReSpeaker 2-Mic HAT, horizontal display, speaker.

### Milestones

#### M1.1 — Order and unbox Phase 1 hardware
- [ ] Order Phase 1 hardware (~€180)
- [ ] Pi 5 set up with Raspberry Pi OS 64-bit
- [ ] SSH access from main PC
- [ ] First X post with hardware unboxing

#### M1.2 — Audio on Pi
- [ ] ReSpeaker 2-Mic HAT installed and tested
- [ ] Audio capture working at 16kHz
- [ ] Speaker + amp working
- [ ] Echo test: speak → recorded → played back

#### M1.3 — Display on Pi
- [ ] Horizontal IPS LCD connected and showing image
- [ ] Pygame window rendering at full screen
- [ ] Port the Phase 0 face animation to the Pi

#### M1.4 — Bridge Server architecture
- [ ] Refactor: split Pi-side code (audio, face) from PC-side code (STT, TTS, OpenClaw orchestration)
- [ ] FastAPI Bridge Server runs on PC, exposes endpoints
- [ ] Pi sends audio chunks via HTTP, receives transcribed text + TTS audio back
- [ ] Bridge Server runs as a `systemd` service on the PC

#### M1.5 — Wake word
- [ ] openWakeWord installed on Pi
- [ ] Pre-trained "Hey Jarvis" model working
- [ ] Pi only triggers when wake word detected (not on any sound)

#### M1.6 — Temporary enclosure
- [ ] Cardboard or simple 3D-printed cylinder housing
- [ ] All components fit inside
- [ ] Cables routed cleanly
- [ ] Demo video showing the device working
- [ ] X post: "It's alive! Phase 1 done"

### Phase 1 Done Criteria
- Standalone device on the desk responds to voice via wake word.
- Same voice loop as Phase 0, but on dedicated hardware.
- The PC only runs OpenClaw + Bridge Server.

---

## Phase 2 — Vision: Adding the Camera

**Duration:** 3–4 weeks
**Goal:** Add camera capability. No movement yet — the camera is fixed for now. Validate vision pipeline before adding mechanical complexity.
**What's new:** Pi Camera Module 3, vision API integration.

### Milestones

#### M2.1 — Order Phase 2 hardware
- [ ] Order Pi Camera Module 3 + ribbon cable (~€35)
- [ ] Camera mounted on the static enclosure (front-facing)
- [ ] Camera ribbon routed cleanly through the Pi

#### M2.2 — Camera basics
- [ ] `picamera2` installed and working
- [ ] Capture a still image, save to file
- [ ] Capture video stream at 720p, view in real time

#### M2.3 — Vision pipeline
- [ ] Bridge Server endpoint to receive camera frames from Pi
- [ ] Send frames to vision API (Claude / GPT-4o)
- [ ] Receive image description back
- [ ] Test with simple queries: "What do you see?", "What am I holding?"

#### M2.4 — Face detection (no tracking yet)
- [ ] OpenCV face detection running on Pi
- [ ] Real-time face detection with bounding boxes
- [ ] Display face count or position in logs
- [ ] No movement yet — just detection

#### M2.5 — Privacy and visual indicator
- [ ] LED on Pi blinks when camera is active
- [ ] Add option to disable camera in software
- [ ] Document privacy posture clearly

#### M2.6 — Vision integrated with voice
- [ ] When user asks "what do you see?", camera captures + vision API processes + TTS speaks the answer
- [ ] Demo video: ask the device to describe its surroundings
- [ ] X post: "Now it sees too"

### Phase 2 Done Criteria
- The device captures images on demand.
- Vision queries work end-to-end through OpenClaw.
- Face detection runs reliably; we know where faces are in the frame.
- Camera can be physically/digitally disabled for privacy.

---

## Phase 3 — Movement: 2-Axis Gimbal Head

**Duration:** 4–6 weeks
**Goal:** The most visually impactful phase. The head moves. It looks at you. It nods, shakes, follows.
**What's new:** Pan + tilt servos, gimbal bracket, servo controller, sound + face tracking.

### Milestones

#### M3.1 — Order Phase 3 hardware
- [ ] Order servos, bracket, PCA9685, external power (~€110)
- [ ] Test servos manually before mounting

#### M3.2 — Mechanical mount
- [ ] Pan-tilt bracket assembled
- [ ] Servos installed on bracket with proper screws and arms
- [ ] Bracket mounted on top of body
- [ ] Display + camera moved from static mount to gimbal head
- [ ] Cables routed through pan-tilt joint with strain relief

#### M3.3 — Servo control software
- [ ] PCA9685 working over I2C
- [ ] Pan and tilt servos controllable via Python
- [ ] Manual control: keyboard input moves the head
- [ ] Easing functions implemented (smooth movements, no jumps)
- [ ] Calibration: define safe rotation limits for each axis

#### M3.4 — Expressive movements
- [ ] "Nod" gesture (tilt up-down sequence) — for "yes"
- [ ] "Shake" gesture (pan left-right sequence) — for "no"
- [ ] "Look up thinking" (tilt up + slight pan) — for processing state
- [ ] "Look down" (tilt down) — for sad/error states
- [ ] "Curious" (slight pan + tilt combination) — for idle behavior
- [ ] Library of named gestures callable from main code

#### M3.5 — Face tracking with camera
- [ ] Use OpenCV face detection from Phase 2
- [ ] PID controller maps face position in frame to pan-tilt corrections
- [ ] Head smoothly follows the user's face when in view
- [ ] Demo video: walk around the device, head follows you
- [ ] X post: "It tracks you with its eyes" (this is the viral moment)

#### M3.6 — Sound localization (basic)
- [ ] Use 2-mic stereo difference to estimate sound direction
- [ ] When wake word triggers, head turns toward the sound source
- [ ] Combined with face tracking: snap to sound, then refine with face detection

#### M3.7 — Idle behaviors
- [ ] Random small movements when idle (looking around)
- [ ] Slow blinking on the face matches subtle head movements
- [ ] Feels alive, not frozen

### Phase 3 Done Criteria
- Head moves smoothly in 2 axes.
- It tracks faces with the camera.
- It turns toward the speaker.
- Idle behaviors make it feel alive.
- Movements use easing — no robotic jerks.

---

## Phase 4 — Senses + Polish

**Duration:** 4–6 weeks
**Goal:** Add environmental awareness and physical interaction. Upgrade audio. Make it feel like a complete character.
**What's new:** 4-mic array, accelerometer, light sensor, capacitive touch, LED ring, custom wake word, lip-sync.

### Milestones

#### M4.1 — Order Phase 4 hardware
- [ ] Order audio upgrade + sensors + LED ring (~€107)

#### M4.2 — Audio upgrade
- [ ] Replace ReSpeaker 2-Mic HAT with ReSpeaker 4-Mic USB XVF3800
- [ ] Far-field capture validated (clear at 3+ meters)
- [ ] Direction of arrival (DoA) data extracted from the 4-mic array
- [ ] Improve sound localization in Phase 3 with proper DoA

#### M4.3 — Custom wake word
- [ ] Train custom openWakeWord model for the chosen wake phrase
- [ ] Replace "Hey Jarvis" with the custom one
- [ ] Validate false-accept rate < 0.5/hour

#### M4.4 — Lip-sync
- [ ] Mouth/expression syncs with TTS audio amplitude
- [ ] At minimum: open/close on speech peaks
- [ ] Stretch: phoneme-aware mouth shapes

#### M4.5 — Environmental sensors
- [ ] Accelerometer (MPU-6050) reads tilt and motion
- [ ] Light sensor (BH1750) auto-dims display at night
- [ ] React to being moved: surprise face if lifted, dizzy if shaken

#### M4.6 — Physical interaction
- [ ] Capacitive touch sensor on top of head
- [ ] "Petting" gesture triggers happy face animation
- [ ] Optional: idle behavior asks for attention if not interacted with for a while

#### M4.7 — Status feedback
- [ ] NeoPixel LED ring at base
- [ ] LED color/animation reflects state (listening = blue pulse, thinking = white spin, error = red)
- [ ] Echo-style visual cue without needing to look at the face

#### M4.8 — Privacy and reliability
- [ ] Hardware mute switch for mic + camera
- [ ] Robust error handling (network down, OpenClaw unreachable, etc.)
- [ ] Logging to file for debugging
- [ ] Auto-restart on crash via `systemd`

### Phase 4 Done Criteria
- Device feels like a character, not a gadget.
- Custom wake word works reliably.
- LEDs and sensors give the device presence even when idle.
- Better audio means cleaner voice capture.
- Privacy is hardware-enforced.

---

## Phase 5 — Final Aesthetic + Public Launch

**Duration:** 6–10 weeks
**Goal:** Production-quality finish. Refine the enclosure, optimize storage, polish everything, launch publicly.
**What's new:** Final 3D-printed enclosure, NVMe SSD, full polish pass.

### Milestones

#### M5.1 — Final enclosure design
- [ ] CAD design in Fusion 360 / Onshape
- [ ] Iterate based on Phase 1–4 hardware fit
- [ ] Aesthetic refinement: surface treatments, proportions, expressive silhouette
- [ ] Cable management designed in (not afterthought)

#### M5.2 — 3D printing and assembly
- [ ] Print enclosure parts in PLA or PETG
- [ ] Sand and finish surfaces
- [ ] Optional: paint or vinyl wrap for character
- [ ] Final assembly with clean wiring

#### M5.3 — Storage upgrade
- [ ] Install Pi M.2 HAT+ with NVMe SSD
- [ ] Migrate OS from microSD to NVMe
- [ ] Faster boot, more reliable than SD

#### M5.4 — Polish pass
- [ ] Refine all face animations
- [ ] Add personality details (idle behaviors, contextual reactions)
- [ ] Performance optimization (smoother animations, less latency)
- [ ] Final demo video

#### M5.5 — Public launch
- [ ] Comprehensive README with assembly guide
- [ ] STL files and BOM published in repo
- [ ] Launch X post with final demo video
- [ ] Submit to Hacker News, r/raspberry_pi, r/openclaw if it exists
- [ ] Open the project to community contributions
- [ ] Tag a v1.0 release on GitHub

### Phase 5 Done Criteria
- Someone can clone the repo, follow the guide, order the parts, and build their own in a weekend.
- The device is presentable, polished, and people ask about it when they see it.
- Project is fully open source with all documentation public.

---

## Phase 6+ — Advanced (Optional, Future)

Long-term ideas beyond v1.0. No commitment, no timeline.

- **Face recognition** — knows who's speaking, personalized responses
- **Emotion detection from camera** — adapts behavior to user's mood
- **Local LLM** — Hailo AI HAT+ for fully offline inference
- **Multi-device sync** — multiple Companions in different rooms, one OpenClaw brain
- **Battery support** — portable mode
- **More motion** — third axis (roll), or wheels for movement
- **Community marketplace** — skins, voice packs, custom face designs
- **Mobile app** — companion app for configuration and remote interaction
- **Brushless gimbal upgrade** — for true DJI-level smooth movement

---

## Tracking Progress

- Each milestone gets a GitHub issue when started
- Completion = commits merged to main + checkbox ticked here
- X posts mark major phase transitions, not every milestone (avoid noise)
- Demo videos at the end of each phase, embedded in the repo README
- Progress is visible. Slow is fine. Stuck is a problem we solve, not hide.
