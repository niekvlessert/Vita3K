# Ys: Memories of Celceta rendering experiment (macOS ARM64)

This experimental change is based on Vita3K upstream `da5d2e5` (2026-11-14).
It adapts the Celceta-specific Vulkan and shader fixes from
[nckstwrt/Vita3K-Plus d508ff2](https://github.com/nckstwrt/Vita3K-Plus/commit/d508ff2bf3a81c76eeedcfa670645e9b133527f0)
to the current upstream renderer. It is **not a verbatim cherry-pick**.

## Symptom / reproducible test

Game: *Ys: Memories of Celceta*, US title ID `PCSE00245`.
An Apple M1 Mac running Vita3K Vulkan at native (1x) resolution exhibits oversized,
partly transparent scene content and heavy overexposure during gameplay.
High Accuracy on and Surface Sync enabled in settings do not resolve the artifacts.

## Changes

- Reset the GXM Vulkan render target size each scene; reduce it to the
  downscaled color surface when that color surface is half the target size.
- Apply the same per-scene scale to viewport and scissor.
- Reject color surface casts whose source offset lies outside the cached image;
  clamp same-format copies to the remaining source region.
- Derive non-dependent texture query write-component counts from shader
  register and descriptor information instead of trusting texture format alone.

The source fork's pipeline-cache texture-hint tracing is intentionally not copied:
it was disabled behind `constexpr false` and is diagnostic only.
Its old `vk::ImageBlit` cast path has been replaced upstream by `vk::ImageCopy`;
the source-bounds part of the fix is adapted to the latter.

## Validation

The change needs a GitHub Actions macOS ARM64 build and in-game A/B testing.
It should not be considered confirmed to fix Celceta or safe across games before
testing. The main Vita3K installation and game data are not modified by this branch.

The upstream `Build CI` workflow builds with `ci-macos-arm64` and uploads a
`vita3k-<commit>-macos-arm64` artifact. Check the Actions run after pushing.

Recommended baseline: Vulkan, 1x resolution, High Accuracy enabled.
Compare against the original installed build at the same location in gameplay.
