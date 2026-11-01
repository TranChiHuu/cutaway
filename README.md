# Cutaway

A Claude Code plugin that turns any machine into an interactive 3D simulator page you can take apart: live gauges driven by one model, a button per subsystem, cutaways, camera views, a self-running tour, and optionally a 60 fps video with a synced score.

Workflow inspired by Elvar's article ["How I Turn Any Machine Into a 3D Simulator You Can Take Apart"](https://x.com/elvar_official/status/2116174609355571402).

## Install

```
/plugin marketplace add TranChiHuu/cutaway
/plugin install cutaway@tranchihuu
```

## Use

```
/cutaway:build a mechanical watch escapement ticking at 4 Hz, reference: ./escapement.jpg
```

Or just ask: "build a 3D simulator of a car differential on a turn". Always give a reference picture.

## Stages

0. Pick the machine + reference · 1. Model first, then geometry · 2. Console · 3. Look · 4. Critic check · 5. Interactive page · 6. Video · 7. Score · 8. Publish

## License

MIT
