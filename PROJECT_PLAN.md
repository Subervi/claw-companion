# Project: Voice Assistant with Animated Face (OpenClaw Hardware Companion)

> A premium DIY desk companion that talks to OpenClaw via voice, shows animated facial expressions, sees through a camera, rotates its head with a 2-axis gimbal toward the speaker, and reacts to its physical environment. Open source, hackable, designed with character. Built progressively, one capability at a time.

## Vision

Build a physical companion for OpenClaw that goes beyond existing products. Standalone hardware with a microphone array, speaker, animated face display, 2-axis pan-tilt gimbal head, camera, and environmental sensors. The brain (OpenClaw) runs on the user's dedicated PC. The device is a thin client that communicates with that PC via HTTP over LAN.

**Project goals:**
- Match or exceed every capability of existing companion products
- Distinctive, character-driven aesthetic — not a generic box
- Open source from day one — code, hardware schematics, 3D files, all public
- DIY-friendly — buildable with off-the-shelf parts and 3D printing
- Hackable — every layer is modular and swappable
- **Built progressively** — one capability validated before the next is added

---

## Build Philosophy: One Layer at a Time

This project deliberately moves slowly through clear, sequential phases. Each phase adds exactly one new capability on top of a fully working previous phase. This is how serious hardware projects are built.

```
Phase 0: Everything in PC (no hardware)
   ↓
Phase 1: + Hardware (Pi + display + audio)
   ↓
Phase 2: + Camera
   ↓
Phase 3: + Pan-tilt gimbal (head rotation 2 axes)
   ↓
Phase 4: + Environmental sensors and polish
   ↓
Phase 5: + Final enclosure and public launch
```

Why this matters:
- Each phase isolates a single point of failure
- Each phase is a public milestone (X post, demo video)
- Learning is progressive, not overwhelming
- If Phase N has a problem, Phase N-1 is still working
- Enjoyment comes from the process, not just the result

---

## Form Factor

**Cylindrical body with 2-axis gimbal head.** A fixed cylindrical base houses the Pi, speaker, mic array, and most electronics. On top, a 2-axis gimbal mounts the head — the display, camera, and front-facing sensors. The pan servo (horizontal) rotates the head left/right. The tilt servo (vertical) tilts the head up/down. Together they enable expressive movements: looking at the user, nodding ("yes"), shaking ("no"), looking up ("thinking"), looking down ("sad"), and following faces.

```
   ┌──────────────┐
   │ ◉  ^_^   ◉  │  ← head: display + camera + front sensors
   └──────┬───────┘
       ╱ │ ╲       ← tilt servo (up/down)
      └──┬──┘      ← pan servo (left/right)
   ┌─────┴────────┐
   │   ●  ●  ●    │  ← LED ring (status)
   │              │
   │  fixed body  │  ← Pi 5, speaker, mic array, sensors,
   │  (cylinder)  │     M.2 NVMe, power, electronics
   │              │
   │   ▢  privacy │  ← physical privacy switch (mic + cam)
   └──────────────┘
```

**Design references:**
- Pixar Luxo Jr. for character and motion language
- Anki Vector / Cozmo for emotional expression and head movement
- WALL-E / EVE for silhouette personality
- DJI Osmo Pocket for gimbal motion smoothness (aspirational, not literal)

---

## Technical Architecture

```
┌──────────────────────────┐         ┌──────────────────────────┐
│   DEVICE (Pi 5 8GB)      │         │   PC running OpenClaw    │
│                          │         │                          │
│  ┌───────────────────┐  │         │  ┌────────────────────┐ │
│  │ ReSpeaker 4-Mic   │  │         │  │ OpenClaw Gateway   │ │
│  │ Array (XVF3800)   │  │         │  │ :18789             │ │
│  └─────────┬─────────┘  │         │  └─────────▲──────────┘ │
│            ▼             │         │            │             │
│  ┌───────────────────┐  │  HTTP   │  ┌─────────┴──────────┐ │
│  │ openWakeWord +VAD │  │ ◄──────►│  │ Bridge Server      │ │
│  └─────────┬─────────┘  │   LAN   │  │ (FastAPI)          │ │
│            ▼             │         │  │                    │ │
│  ┌───────────────────┐  │         │  │ ┌────────────────┐ │ │
│  │ Audio capture     │──┼─────────┼─►│ │ Whisper STT    │ │ │
│  └───────────────────┘  │         │  │ └────────────────┘ │ │
│                          │         │  │ ┌────────────────┐ │ │
│  ┌───────────────────┐  │         │  │ │ Piper TTS      │ │ │
│  │ Audio playback    │◄─┼─────────┼──│ │  / ElevenLabs  │ │ │
│  └───────────────────┘  │         │  │ └────────────────┘ │ │
│                          │         │  │ ┌────────────────┐ │ │
│  ┌───────────────────┐  │         │  │ │ Vision pipeline│ │ │
│  │ Camera (1080p)    │──┼─────────┼─►│ │ (Claude/GPT-4o)│ │ │
│  └───────────────────┘  │         │  │ └────────────────┘ │ │
│                          │         │  └────────────────────┘ │
│  ┌───────────────────┐  │         │                          │
│  │ Face display      │  │         │                          │
│  │ (touch, IPS)      │  │         │                          │
│  └───────────────────┘  │         │                          │
│                          │         │                          │
│  ┌───────────────────┐  │         │                          │
│  │ Pan-tilt gimbal   │  │         │                          │
│  │ (2 digital servos)│  │         │                          │
│  └───────────────────┘  │         │                          │
│                          │         │                          │
│  ┌───────────────────┐  │         │                          │
│  │ Sensors:          │  │         │                          │
│  │  - accelerometer  │  │         │                          │
│  │  - light sensor   │  │         │                          │
│  │  - capacitive     │  │         │                          │
│  │    touch (head)   │  │         │                          │
│  └───────────────────┘  │         │                          │
│                          │         │                          │
│  ┌───────────────────┐  │         │                          │
│  │ LED ring          │  │         │                          │
│  │ (NeoPixel)        │  │         │                          │
│  └───────────────────┘  │         │                          │
│                          │         │                          │
│  ┌───────────────────┐  │         │                          │
│  │ Privacy switch    │  │         │                          │
│  │ (hardware)        │  │         │                          │
│  └───────────────────┘  │         │                          │
└──────────────────────────┘         └──────────────────────────┘
```

**Why this design:**
- The Pi handles audio I/O, animation, gimbal movement, sensor reading, and basic vision capture only.
- The Bridge Server on the PC owns STT, TTS, vision processing, and orchestration.
- OpenClaw stays untouched. We talk to it through its existing HTTP API.
- Heavy compute (Whisper, vision models) runs on the more capable PC.

---

## Tech Stack

### Hardware (Final BOM, sorted by phase)

| Component | Model | Phase | Approx. price |
|---|---|---|---|
| **Phase 1 — Voice + Display** | | | |
| SBC | Raspberry Pi 5 8GB | 1 | €80 |
| Active cooler | Pi 5 official | 1 | €5 |
| Power supply | Pi 5 official 27W USB-C | 1 | €13 |
| microSD (boot) | 64GB class A2 | 1 | €12 |
| Audio I/O | ReSpeaker 2-Mic Pi HAT | 1 | €15 |
| Face display | Horizontal IPS LCD touch ~3.5" | 1 | €30 |
| Speaker | Mini amp PAM8403 + 3W 4Ω driver | 1 | €10 |
| Cables, jumpers, breadboard | misc | 1 | €15 |
| **Phase 1 subtotal** | | | **~€180** |
| **Phase 2 — Vision** | | | |
| Camera | Pi Camera Module 3 (1080p, autofocus) | 2 | €30 |
| Camera ribbon cable | Long Pi 5 compatible | 2 | €5 |
| **Phase 2 subtotal** | | | **~€35** |
| **Phase 3 — Gimbal Movement** | | | |
| Pan servo | Hitec HS-5485HB or Savox SC-1251MG digital | 3 | €35 |
| Tilt servo | Hitec HS-5485HB or Savox SC-1251MG digital | 3 | €35 |
| Pan-tilt bracket | Metal U-bracket pan-tilt kit | 3 | €15 |
| Servo controller | PCA9685 16-channel I2C driver | 3 | €10 |
| External power for servos | 6V 3A regulated supply | 3 | €15 |
| **Phase 3 subtotal** | | | **~€110** |
| **Phase 4 — Sensors + Audio Upgrade** | | | |
| Audio upgrade | ReSpeaker 4-Mic USB XVF3800 | 4 | €85 |
| Accelerometer | MPU-6050 (3-axis) | 4 | €5 |
| Ambient light sensor | BH1750 | 4 | €3 |
| Capacitive touch | TTP223 modules | 4 | €3 |
| LED ring | NeoPixel 16-LED ring | 4 | €8 |
| Privacy switch | Slide switch (mic + cam cutoff) | 4 | €3 |
| **Phase 4 subtotal** | | | **~€107** |
| **Phase 5 — Final Build** | | | |
| M.2 HAT + NVMe SSD | 256GB NVMe + Pi M.2 HAT+ | 5 | €40 |
| 3D printing filament | PLA/PETG for final enclosure | 5 | €15 |
| Hardware finishing | Sandpaper, paint, etc. | 5 | €15 |
| **Phase 5 subtotal** | | | **~€70** |
| **Total project cost** | | | **~€502** |

> Note: building progressively means you can stop at any phase and still have a working product. Phase 1 alone gives you a voice assistant. Phase 3 gives you a moving head. Each phase is incrementally rewarding.

### Software

**On the PC (brain):**
- OpenClaw (already installed)
- Python 3.11+
- `faster-whisper` (local STT, optimized version of Whisper)
- `piper-tts` (local TTS) or ElevenLabs API (premium option)
- `fastapi` + `uvicorn` (Bridge Server)
- `httpx` (HTTP client to call OpenClaw)
- Vision API client (Claude / GPT-4o for image understanding)

**On the Pi (client):**
- Raspberry Pi OS 64-bit
- Python 3.11+
- `sounddevice` (audio capture/playback)
- `openWakeWord` (wake word detection)
- `pygame` (face animation, RoboEyes-inspired but horizontal)
- `picamera2` (Pi camera control)
- `opencv-python` (face detection for tracking)
- `adafruit-pca9685` (servo controller via I2C)
- `gpiozero` (LEDs, basic GPIO)
- `requests` or `httpx` (HTTP client to the Bridge)
- `smbus2` (I2C sensors)

---

## Working Principles

1. **Document everything.** Every decision, every problem, every fix. The repo is the journal.
2. **Fast iteration over perfection within each phase.** Phase outputs may be ugly; that's fine. They must work.
3. **One phase at a time. Always.** Don't start Phase N+1 until Phase N is complete and stable.
4. **Privacy by default.** Local wake word, local audio when possible. Hardware kill switch for mic + camera. Only OpenClaw decides what reaches the cloud.
5. **Validate before upgrading.** Cheap parts first when possible. Only upgrade when we know what's needed.
6. **Open source, every piece.** Code, schematics, 3D files, BOM. If someone wants to build their own, they should be able to in a weekend.
7. **Aesthetics matter.** A companion robot lives on someone's desk. It must have character, not look like a server.
8. **Enjoy the process.** The journey is the project. Each phase teaches us something.

---

## Differentiators vs. Existing Products

| Feature | ClawStage ($399) | This Project | Notes |
|---|---|---|---|
| OpenClaw integration | ✅ Native | ✅ Via Bridge Server | Both work |
| Face animation | Generic | Distinctive | Custom design language |
| Head rotation (1 axis) | ✅ | ✅ | Same capability |
| Head tilt (2 axes total) | ❌ | ✅ | More expressive movement |
| Sound localization | ✅ | ✅ | Same capability |
| Camera face tracking | ❌ (camera but no tracking) | ✅ | Looks at faces, not just sound |
| Camera | ✅ | ✅ | Pi Camera Module 3 |
| Touch screen | ✅ | ✅ | Same approach |
| Accelerometer | ✅ | ✅ | Reactive to physical movement |
| Light sensor | ✅ | ✅ | Auto-dim, ambient awareness |
| Privacy switch | ✅ | ✅ | Hardware mic + cam cutoff |
| LED status ring | ❌ | ✅ | Echo-style visual feedback |
| Capacitive "petting" | ❌ | ✅ | Adds character |
| Open source code | ❌ (only README) | ✅ Full source | Real open source |
| Open hardware files | ❌ | ✅ STL + schematics | Buildable by anyone |
| Custom enclosure | ❌ | ✅ | Aesthetic differentiator |
| Hackable | Limited | ✅ Fully | Modular, every layer swappable |
| Price | $399 USD | ~€500 (DIY) | Build it yourself, learn everything |

---

## Known Risks

| Risk | Mitigation |
|---|---|
| Total latency feels too high | Whisper on GPU if PC has one, or Whisper API. Bridge on same LAN, not Wi-Fi. |
| ReSpeaker 2-Mic struggles with noise | Phase 4 upgrade to 4-Mic XVF3800. |
| Pi 5 too weak as we add features | Migrate to Orange Pi 5 Plus (RK3588 + NPU) or Intel N100 mini PC. |
| OpenClaw API changes | The Bridge Server abstracts this. We only update the bridge. |
| Local TTS quality not good enough in Spanish | Plan B: ElevenLabs API (paid, but sounds great). |
| Sound localization is too imprecise | Combine with vision tracking for better targeting. |
| Servo movement feels mechanical / janky | Use easing functions, slow movements, digital metal-gear servos. |
| Servo power draw too high for Pi | External 6V regulated supply for servos via PCA9685. |
| Camera adds complexity | Camera is its own dedicated phase (Phase 2). |
| Heat in compact enclosure | Active cooler, ventilation in 3D design. |
| Servo noise | Digital metal-gear servos are quieter than basic SG90. |

---

## Inspiration & Reference Projects

- **OpenClaw** — the brain we're embodying ([openclaw.ai](https://openclaw.ai))
- **ClawStage** by HooRii — official OpenClaw hardware companion, $399 ([github.com/HooRii-OT/clawstage](https://github.com/HooRii-OT/clawstage)). Reference for hardware specs.
- **Tabbie / Taby** — desk robot with face animations ([tabbie.me](https://tabbie.me) / [github.com/Peeeeteer/tabbie](https://github.com/Peeeeteer/tabbie)). Inspiration for face state design.
- **FluxGarage RoboEyes** — robot eyes animation library ([github.com/FluxGarage/RoboEyes](https://github.com/FluxGarage/RoboEyes)). Animation style reference.
- **openWakeWord** — open source wake word detection ([github.com/dscripka/openWakeWord](https://github.com/dscripka/openWakeWord)).
- **Anki Cozmo / Vector** — gold standard for character animation in companion robots.
- **Eilik** by Energize Lab — current best-in-class consumer companion robot.
- **DJI Osmo Pocket** — aspirational reference for gimbal smoothness.
- **Pimoroni Pan-Tilt HAT** — proven pan-tilt mechanism ([learn.pimoroni.com](https://learn.pimoroni.com/building-a-pan-tilt-face-tracker)).

---

## Roadmap

See [ROADMAP.md](./ROADMAP.md) for detailed phase planning, milestones, and timeline.

---

## Immediate Next Steps

1. ✅ Define form factor (cylindrical body + 2-axis gimbal head)
2. ✅ Define full feature list
3. ✅ Define phased build order (one capability at a time)
4. Create the GitHub repo `claw-companion` (public, no promotion)
5. Add `PROJECT_PLAN.md` and `ROADMAP.md` to the repo
6. Confirm OpenClaw Gateway is reachable (`http://127.0.0.1:18789`)
7. Investigate the exact Gateway API
8. Create the X account as a development journal
9. Kick off Phase 0
