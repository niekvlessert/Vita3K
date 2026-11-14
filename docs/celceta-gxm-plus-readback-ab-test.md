# Isolated Ys: Memories of Celceta macOS test

**A/B experiment, not a production-ready fix.**

This branch combines **only**:
- GXM render-target downscaling from upstream draft PR [#4421](https://github.com/Vita3K/Vita3K/pull/4421)
  (original implementation credited to nckstwrt / Vita3K-Plus)
- Proposed macOS Vulkan unmapped guest framebuffer readback from upstream
  PR [#4355](https://github.com/Vita3K/Vita3K/pull/4355), authored by enthhende

**It excludes** the experimental shader non-dependent query component-count
adjustment and cached color surface copy-bounds patch.

## Prior observations

1. Original baseline: severe ghosting and blown-out lighting.
2. GXM downscale + shader + color copy (commit 7d26baa):
   ghosting gone, severe overexposure remains, regardless of High Accuracy.
3. Same three changes plus proposed readback (commit b5f77bd):
   user reported full visual correction.
4. GXM downscale **alone** (commit ebb44d3):
   no obvious oversized ghosting, but overexposure is back; game log confirms
   GXM target changed from 512x204 to 256x102.

This experiment determines whether GXM downscale + PR #4355 readback,
*without* the shader and texture-copy changes, is sufficient for correct
Celceta rendering.

## Test conditions

macOS Apple M1, Vulkan, native 1x resolution, High Accuracy off,
Surface Sync enabled in settings; guest GPU memory mapping disabled.
Do not overwrite the known-good emulator build or save data. Back up user data
before running this experimental memory-handling code.

In the log, inspect `Vulkan unmapped surface readback succeeded` and
`Vulkan unmapped surface readback unavailable`. A single unsupported
surface does not establish that all requested readbacks failed.
