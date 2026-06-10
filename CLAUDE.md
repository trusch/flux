# FLUX — project context

Single self-contained `index.html` — mobile-first showcase app (GPU Navier-Stokes fluid sim + generative WebAudio music). First of the single-prompt AI showcase series (siblings: `~/projects/portal`, `~/projects/echo`).

## Invariants

- ONE file, zero dependencies, no build step. Everything inline.
- Public repo `trusch/flux`, deployed via GitHub Pages from `main` root. Pushing to `main` is allowed for this repo.
- Framebuffers must survive resizes via copy-blit (`resizeDoubleFBO`) — mobile URL-bar show/hide fires resizes constantly; recreating FBOs wipes the user's painting.
- Audio: keep the unlock dance (suspended AudioContext + gesture-end resume kicks), the iPad audio-session promotion (audioSession.type='playback' + a looping silent <audio> keepalive that MUST stay gated to Apple touch devices and >=1s long — un-gated it crackles on Android, see 500ce55; without it the iPad mute switch silences WebAudio). The pluck synth is intentionally UNBOUNDED here (no polyphony cap — bounding caused note drops, see 9eb8f08/500ce55), AND the activity-gated drone (`droneEnv` multiplies base + LFO; fades out ~8s after last pointer interaction, autopilot does not count).

There are THREE deployed files in this repo: `index.html` (FLUX), `original.html` (frozen day-one build for A/B debugging — never edit), `nebula.html` (NEBULA: 3D solver in a 512² tiled atlas of 64³ voxels, toroidal wrap, volume raymarch + analytic planets; player = ink cloud bound by a force pass; MindHub epic nebula-3d-flux-flight-demo-*).

## NEBULA invariants
- GLSL is ES 1.00: array indices MUST be constant-index-expressions (uSph[hitId] broke the whole render once — fetch via constant loop).
- The shader strings interpolate ${ATLAS} — when editing via Bash heredocs, beware over-escaping (a literal "\${ATLAS}" in the file kills the shader and the screen silently shows the previously-bound pass).
- Atlas layout (64³ → 8×8 tiles of 64²) must stay identical across all passes.

## Verification

- Syntax: `awk '/<script>/{f=1;next}/<\/script>/{f=0}f' index.html > /tmp/f.js && node --check /tmp/f.js`
- Render: headless chromium (`--use-angle=swiftshader --enable-unsafe-swiftshader --virtual-time-budget=...`) on a /tmp copy with injected auto-ignite; SwiftShader runs ~3fps, so probe framebuffers with readPixels rather than trusting screenshots.
- NEVER pass `--screenshot=/dev/null` — chromium aborts the page run silently and every check passes vacuously. Always use a real path.
- Always assert program LINK_STATUS in probes (a failed link makes blit(null) draw with the previously bound program — looks like "wrong pass on screen", not like an error).
