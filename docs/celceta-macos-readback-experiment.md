# Celceta / macOS GPU surface readback experiment

**Status: diagnostic only.** Do not treat this build as game-compatible
until it has been tested. Do not overwrite another version or save data.
This branch builds on the already successful Celceta ghosting fix
(`7d26baa`) and applies the *proposed, not yet merged* upstream
[PR #4355](https://github.com/Vita3K/Vita3K/pull/4355),
head revision `efba566`, authored by enthhende.

## Hypothesis

The separate Celceta ghosting fix addresses mismatched render-target
dimensions. The remaining bright/washed-out graphics may arise because the
game expects GXM color surface synchronization into CPU-visible guest memory.
The official Android release notes explicitly mention surface sync fixing
Ys: Memories of Celceta brightness.

Vita3K's macOS Vulkan backend disables guest GPU memory mapping, making its
normal surface-sync path unavailable even when the UI setting is enabled.

PR #4355 implements conservative synchronous readback for **supported,
linear** color formats (RGB565, RGB24, RGBA32) when a guest
`SyncSurfaceData` command is submitted. Other formats and tilings are
unsupported; Celceta's specific post-processing surface usage has not
yet been confirmed. The upstream PR tests and other game results do not
prove a Celceta fix.

## Test

1. Launch the GitHub Actions macOS ARM64 build from this branch as an
   isolated experimental app. Preserve a backup of your Vita3K data
   and any game saves before testing unreviewed memory-handling code.
2. Test `PCSE00245` in the same outdoor scene at native 1x resolution.
3. Start with Vulkan, Surface Sync enabled, High Accuracy **off**
   (same as the last screenshot).
4. Compare both brightness and absence of ghosting with the proven
   `7d26baa` build, and report the outcome.
5. Check `vita3k.log` for `Unmapped surface readback` warnings.

This experiment intentionally limits CI to a macOS ARM64 job.
It is **not** proposed as an upstream pull request.
