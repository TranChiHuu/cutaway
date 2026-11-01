# Cutaway

A Claude Code plugin that turns any machine into an interactive 3D simulator you can take apart.

You name a machine and attach a reference picture. Claude builds one offline HTML page where the machine runs, every subsystem has a button that isolates or cuts it open, live charts read the same model that moves the parts, and a cursor tours the page by itself until the visitor takes over. If you want, it also records the tour to a 60 fps video with a score synced to the motion.

Workflow inspired by Elvar's article ["How I Turn Any Machine Into a 3D Simulator You Can Take Apart"](https://x.com/elvar_official/status/2116174609355571402).

## Install

In a Claude Code session:

```
/plugin marketplace add TranChiHuu/cutaway
/plugin install cutaway@tranchihuu
```

Or from your shell:

```bash
claude plugin marketplace add TranChiHuu/cutaway
claude plugin install cutaway@tranchihuu
```

No GitHub SSH key? Set `CLAUDE_CODE_PLUGIN_PREFER_HTTPS=1` before adding the marketplace.

### Turn on auto-update

Claude Code doesn't auto-update third-party marketplaces until you turn it on. Do it once:

1. Run `/plugin`.
2. Open the **Marketplaces** tab and select **tranchihuu**.
3. Select **Enable auto-update**.

Claude Code then checks for new commits in the background when a session starts. Every push to `main` is a new version, because the plugin tracks commits instead of a version number.

To update by hand instead:

```
/plugin marketplace update tranchihuu
```

or `claude plugin update cutaway@tranchihuu` from your shell.

## Requirements

- Claude Code. The page itself needs nothing else; it loads three.js from a CDN.
- Node.js 20+ and Playwright with Chromium, for the stills in the critic check and for the video.
- ffmpeg and Python 3, only for the video and score (optional).

Claude installs what's missing when a stage needs it.

## Use

Run the command with a machine and a reference:

```
/cutaway:build a mechanical watch escapement ticking at 4 Hz, reference: ./escapement.jpg
```

Or just ask in plain words. The skill triggers on its own:

> Build a 3D simulator of a car differential on a turn. Here's a cutaway from the manual: ./diff.png

Output goes to `./sim-out/<machine>/index.html`. Open it in a browser.

### Pick a good machine

The reference matters more than anything else. Without one, the result is a generic toy. The best picks have:

- **one moving input**: a crank angle, a stroke from 0 to 1, a lever;
- **real published numbers**: gear teeth, link lengths, bore and stroke;
- **one picture**: a manual cutaway, a photo or a video clip.

Ideas: a bicycle derailleur across 11 sprockets, a sewing machine making a lockstitch, an excavator arm digging one bucket, a V8, a Strandbeest leg.

### The stages

Claude stops after each stage to show you the result. Reply "next", or ask for changes.

| # | Stage | What you get |
|---|-------|--------------|
| 0 | Machine + reference | Confirms the input, the real numbers and their sources |
| 1 | Model, then machine | Pure functions of one input, a hand check of the numbers, geometry driven by the model |
| 2 | Console | Big live number, live charts, a 2D schematic, a button per subsystem, camera views, callouts |
| 3 | Look | Light studio, one accent colour for the working part, readable materials |
| 4 | Critic check | Stills of every beat, read for collisions and wrong numbers, then fixed |
| 5 | Interactive page | Self-running tour, hand-off on any input, quality steps for integrated GPUs |
| 6 | Video *(optional)* | 60 fps 1080p MP4 rendered frame by frame, plus a contact sheet |
| 7 | Score *(optional)* | Procedural audio on the same timeline, mastered to −14 LUFS |
| 8 | Publish *(optional)* | Page under 1 MB, 1200×630 preview card, deploy |

Bonus: ask for a slider that pushes the machine past its limit ("add a wind slider from 0 to 15 m/s") to show what each number does.

### Tips

- Paste real numbers into the prompt when you have them. Claude flags any number it had to guess.
- If parts clip through each other, say so. The skill fixes collisions with parameters, never by moving the camera.
- To skip ahead, say "build stages 1–3 in one go".

## Uninstall

```
/plugin uninstall cutaway@tranchihuu
```

## License

MIT
