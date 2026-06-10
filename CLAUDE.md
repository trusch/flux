# FLUX — project context

Single self-contained `index.html` — mobile-first showcase app (GPU Navier-Stokes fluid sim + generative WebAudio music). First of the single-prompt AI showcase series (siblings: `~/projects/portal`, `~/projects/echo`).

## Invariants

- ONE file, zero dependencies, no build step. Everything inline.
- Public repo `trusch/flux`, deployed via GitHub Pages from `main` root. Pushing to `main` is allowed for this repo.
- Framebuffers must survive resizes via copy-blit (`resizeDoubleFBO`) — mobile URL-bar show/hide fires resizes constantly; recreating FBOs wipes the user's painting.
- Audio: keep the unlock dance (suspended AudioContext + gesture-end resume kicks), the iPad audio-session promotion (audioSession.type='playback' + looping silent <audio> keepalive — without it the mute switch silences WebAudio), AND the activity-gated drone (`droneEnv` multiplies base + LFO; fades out ~8s after last pointer interaction, autopilot does not count).

## Verification

- Syntax: `awk '/<script>/{f=1;next}/<\/script>/{f=0}f' index.html > /tmp/f.js && node --check /tmp/f.js`
- Render: headless chromium (`--use-angle=swiftshader --enable-unsafe-swiftshader --virtual-time-budget=...`) on a /tmp copy with injected auto-ignite; SwiftShader runs ~3fps, so probe framebuffers with readPixels rather than trusting screenshots.
