---
name: cutaway
description: Build an interactive 3D simulator of a real or imagined machine (engine, landing gear, linkage, gearbox, watch escapement, robot, creature) as one offline HTML page the visitor can orbit, cut open and take apart, with live charts driven by the same model that moves the parts, one button per subsystem, camera views and a self-running tour; optionally record it to a 60 fps video with a synced procedural score. Use when someone asks how a machine works, wants a visual explainer, simulator, cutaway, exploded view or interactive 3D lesson, or wants a mechanism shown working rather than described.
---

# Cutaway

One machine, one HTML page. The visitor watches it run, presses a button, and the part doing the work opens up while the gauges around it read real numbers. Left alone, the page tours itself.

Output goes to `./sim-out/<machine-slug>/` in the current folder. Stack: three.js from a pinned CDN version (e.g. `three@0.160.0` via import map), plain HTML/CSS/JS, no build step. Video needs Node 20+, Playwright (Chromium) and ffmpeg; the score needs Python 3 stdlib. Install them only when the user reaches that stage.

## The six parts every simulator has

1. **The machine** — built from code in real units (metres, mm). No imported meshes unless the user supplies them.
2. **The model** — pure functions of one input (crank angle, stroke 0..1, time). Returns every pose and every number. Written and checked *before* any geometry.
3. **The panels** — live charts and readouts that redraw from the model each frame.
4. **The buttons** — one per subsystem (isolate / cut / show alone), camera views, sliders for the inputs.
5. **The window** — the 3D view shows the model at work: traces, markers, ghosted target positions.
6. **The tour** — a scripted cursor drives the page; any input hands control to the visitor; 25 s idle hands it back.

## Stage 0 — Pick the machine and get a reference

This decides everything. Without a reference, models come out as generic toys. Insist on one picture: a manual cutaway, a photo, a clip. Best candidates have **one moving input and real published numbers** (gear teeth, stroke, link lengths, bore). If a number is not sourced, say so.

## Stage 1 — Model first, then the machine

- Write `model(input) -> state` as pure functions. Include every joint pose and every quantity a panel will show.
- Use real numbers and **show the hand check** (e.g. 32 teeth × 4 mm module = 128 mm pitch diameter; half its circumference = 201 mm of rack travel = 180° of leg swing).
- Linkages: solve joints by circle intersection from the link lengths. Gears: ratios from tooth counts. Engines: slider-crank kinematics, 720° cycle.
- Then build geometry. Every part's transform is read from `state`. **Nothing is animated by hand** — twelve hand-animated parts are twelve animations, not a mechanism.

## Stage 2 — The console (1600×900 layout)

- Title + the main live number top-left, big (rpm, stroke %, speed).
- 2+ live charts that move with the machine (e.g. valve lift over 720°, a P–V loop that visibly closes each cycle).
- A 2D schematic panel redrawn from the same model.
- One button per subsystem: isolate it, cut it, show it alone; the rest of the body steps aside.
- Camera-view buttons (hero, front, side, top, inside).
- Callouts with thin leader lines into the parts they name.
- No number on screen is typed in; all come from the frame's `state`.

## Stage 3 — The look

- Light studio background, soft shadows on an invisible floor, **one accent colour** for the part working right now.
- Machine fills 60–70% of its view.
- Dark parts glossy, liners as glass, metal roughness ≥ 0.45 so highlights never streak white.
- Near-white parts get a thin ink outline or a darker ground (white tubes vanish on cream).
- Pick the camera for the mechanism: if it collapses into a lattice face-on, use a 3/4 view.

## Stage 4 — Check it like a critic (mandatory before showing anything)

1. Render a still at every beat of the tour (Playwright screenshot) and **read each image**: parts passing through each other, parts floating off mounts, labels covering what they name, numbers disagreeing with the pose.
2. Scrub every transition in 0.2 s steps and look for jumps (e.g. legs teleporting on a mode change — target the next real position instead).
3. Fix collisions with a number in the parameter block, never by hiding them with the camera.
4. Report what was checked, what was fixed, and show the stills.

## Stage 5 — A page people drive

- Tour plays by itself with a visible cursor that walks to each button and presses it.
- Any click, drag, scroll or key hands control to the visitor; 25 s of quiet hands it back.
- Drag to orbit, scroll to zoom, one key hides the panels.
- Warm/compile every shader before the first frame (`renderer.compile`) so the page never freezes.
- Integrated-GPU quality steps, applied automatically when frame time is high: no ambient occlusion, lower pixel ratio, smaller shadow maps, panels redrawn every other frame. Look for shaders recomputing the same value per vertex.

## Stage 6 — Video (optional)

- `render(t)` must be a pure function of time. The tour is a timeline, not timers.
- A Node script opens the page in headless Chromium, sets `t = frame/60`, screenshots each frame at 1920×1080, pipes PNGs into `ffmpeg -f image2pipe -framerate 60 -i - -c:v libx264 -pix_fmt yuv420p -movflags +faststart`. A screen recorder drops frames; a seeked render cannot.
- Produce a contact sheet of every beat (`ffmpeg ... -vf "select=...,tile=4x3"`) and read it.

## Stage 7 — Score (optional)

- Generate audio in Python stdlib (`wave`, `math`, `struct`) on the **same timeline**: a chord bed, a soft hit on each event the model emits (power stroke, tooth engagement), a click on each tour button press, a riser under input drags.
- Master to −14 LUFS, true peak under −1 dB (`ffmpeg -af loudnorm=I=-14:TP=-1`), mux into the video.

## Stage 8 — Publish (optional)

- Keep the HTML under 1 MB; move any large asset to a file beside it (social sites drop link cards on heavy pages).
- 1200×630 preview card from the best frame, `og:image` with an absolute URL.
- Deploy where the user wants (Vercel, Cloudflare Pages, GitHub Pages).

## Bonus — Let them break it

Add one slider for an outside force (wind, load, throttle) beyond the safe range and run the model live, showing the effect on the same panels. Whoever can push the machine past its limit learns what every number does.

## Rules

- Model before geometry. One input drives everything.
- Real units, real numbers, shown checks; flag any guessed number.
- Stop after each stage and show the user the result before moving on.
