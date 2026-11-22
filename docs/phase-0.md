# Phase 0: Proof of speed

**Goal:** prove the two things most likely to sink the project, before building any editing features:

1. Hardware-decoded video plays smoothly on both platforms' low-end machines.
2. A native GPU viewer can sit inside the Tauri window, over the web interface, without copying frames through the CPU.

**Gate to move on:** a 1080p H.264 clip plays at 30 fps with no dropped frames for 60 seconds on both test machines, inside the app's viewer, and the benchmark numbers below pass.

Work through the tasks in order. Stop and report to Alex at the two checkpoints.

## Tasks

- [ ] **0.1 Scaffold.** Cargo workspace with the crates in `CLAUDE.md`; Tauri 2 app with a React + TypeScript + Vite UI in `ui/`. Load `design/tokens.css` in the UI. A blank window shows the header and an empty viewer area styled with the tokens.
- [ ] **0.2 Continuous builds.** GitHub Actions on every push: Windows and macOS (Apple Silicon) runners; build, `cargo test`, clippy, UI lint; upload unsigned installers as build artifacts so Alex can download a Mac build without building locally.
- [ ] **0.3 FFmpeg.** Add an LGPL shared FFmpeg build for each platform, with a script that fetches or builds it reproducibly (Windows: a prebuilt LGPL shared package; macOS: build from source with `--enable-shared --enable-videotoolbox` and without `--enable-gpl`). Record version and flags in `THIRD_PARTY.md`. Verify in a test that the linked FFmpeg reports an LGPL license.
- [ ] **0.4 Test media.** A script that generates test clips with FFmpeg's built-in test sources: 1080p30 H.264, 4K30 HEVC 8-bit, 4K30 HEVC 10-bit, a 48 kHz stereo tone. Generated files are not committed. Alex adds real phone clips locally in `testdata/local/` (git-ignored).
- [ ] **0.5 Probe and decode.** `media` opens a file, lists streams, and decodes frames using D3D11VA (Windows) or VideoToolbox (macOS), falling back to software. Log which path was used. Tests cover all generated clips.
- [ ] **0.6 Render.** `render` creates a wgpu device, imports decoded NV12 and P010 frames, converts YUV to RGB in a shader (BT.709; BT.2020 tone-mapped to SDR for 10-bit HDR), and scales to the viewer size. Golden-frame test against a reference image.
- [ ] **CHECKPOINT A.** Report to Alex: can decoded frames reach wgpu without a CPU copy on each platform? If not, what the copy costs. Wait for his go-ahead.
- [ ] **0.7 Native viewer.** `viewer` creates a native GPU surface over the web view's viewer area: a child window on Windows, a Metal-backed view on macOS. The UI sends the viewer rectangle on every resize and layout change; the surface follows it, and hides when a dialog covers it.
- [ ] **CHECKPOINT B.** If the overlay can't be made reliable after two serious attempts, stop. Write up what failed and the fallback options (for example, a fully native interface in Slint or egui) in `docs/decisions.md`, and ask Alex to choose.
- [ ] **0.8 Playback.** `playback` keeps a clock driven by audio output (cpal), a small decoded-frame queue, and A/V sync. Space plays and pauses; J/K/L shuttle; clicking the scrub bar seeks.
- [ ] **0.9 Benchmark.** `bench` runs headless on a file and reports JSON: decode fps, dropped frames over 60 s of playback, peak memory, time to first frame, seek-to-frame time. CI runs it on generated clips and stores the results; a regression over 10% fails the build.
- [ ] **0.10 Gate run.** Alex runs the release build on his MSI (app set to "Power saving" in Windows graphics settings so it uses the Intel GPU) and on a Mac. Record the numbers in `docs/benchmarks.md`.

## Acceptance numbers

| Test | Pass |
| --- | --- |
| 1080p30 H.264, 60 s in the viewer | 0 dropped frames, both machines |
| 4K30 HEVC 8-bit decode (hardware) | at least 30 fps on both machines, or a clear report that a proxy is needed |
| Peak memory during the 1080p test | under 1.5 GB |
| Cold start to the window showing | under 3 s |
| Seek to a random frame | under 100 ms |

## Not in Phase 0

Timeline, editing, import UI, proxies, export, captions. Phase 1 starts those.
