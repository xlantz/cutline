# Cutline: instructions for Claude Code

Cutline (working name) is a lightweight video editor for **Windows and macOS**. It must edit 1080p and 4K footage smoothly on modest hardware: a laptop on integrated graphics, or an 8 GB Apple Silicon Mac. It is a focused alternative to DaVinci Resolve for creators, not a replacement for it.

The owner is Alex. He directs the project and tests builds; he is not a full-time developer. Explain decisions in plain language, keep him informed of tradeoffs, and **ask before** adding a dependency, changing the architecture, or deviating from this file.

Read `docs/roadmap.md` for scope and phases, and `docs/phase-0.md` for the current phase.

## Fixed decisions

| Layer | Choice |
| --- | --- |
| App shell | Tauri 2 (WebView2 on Windows, WKWebView on macOS) |
| Interface | React + TypeScript + Vite, in `ui/` |
| Engine | Rust (stable, edition 2021 or newer), a Cargo workspace |
| Decode / encode | FFmpeg **LGPL shared build**, via Rust bindings |
| Hardware decode | D3D11VA on Windows, VideoToolbox on macOS, software fallback |
| Hardware encode | NVENC, Quick Sync, AMF on Windows; VideoToolbox on macOS |
| GPU work | wgpu (Direct3D 12 on Windows, Metal on macOS) |
| Audio out | cpal |
| Captions (Phase 2) | whisper.cpp |
| Project file | SQLite |

## Repository layout

```
cutline/
  src-tauri/        Tauri 2 shell: window, commands, events
  crates/
    engine/         timeline model, edit operations, undo/redo, project file. Pure Rust, no I/O to GPU.
    media/          FFmpeg wrapper: probe, hardware decode, encode, proxies, thumbnails, waveforms
    render/         wgpu render graph: YUV to RGB, scaling, compositing, color, titles
    viewer/         native GPU surface laid over the web view (all platform code lives here)
    playback/       clock, frame queue, A/V sync, audio output
    bench/          benchmark harness (see Performance)
  ui/               React app; talks to Rust only through Tauri commands and events
  design/           tokens.css and tokens.json from the Cutline Design System
  docs/             roadmap, phase specs, decisions log
  THIRD_PARTY.md    every bundled library, version and license
```

## Rules

1. **The interface never touches video.** The UI sends commands and draws panels. All decoding, rendering and playback happen in Rust, off the UI thread.
2. **Never decode or render at full resolution for preview.** Preview resolution = viewer size, capped by the preview quality setting (default 540p on integrated graphics). Full resolution only at export.
3. **Frames stay on the GPU.** Decode into GPU textures and import them into wgpu without a CPU round trip where the platform allows. If a CPU copy is unavoidable, say so in `docs/decisions.md` and measure its cost.
4. **Platform code goes behind traits** (`HwDecoder`, `NativeSurface`, `HwEncoder`) in `media/` and `viewer/`. Everything else is shared, and every feature must work on both Windows and macOS.
5. **FFmpeg stays LGPL.** Never configure with `--enable-gpl` or `--enable-nonfree`; no libx264, libx265 or other GPL libraries. Link dynamically. Record the exact FFmpeg version and configure flags in `THIRD_PARTY.md`. (Alex may sell the app later.)
6. **Originals are never modified.** Proxies, caches and the project file live in the project folder or the app cache.
7. **Use the design tokens.** UI colors, type, spacing and radii come from `design/tokens.css` (`var(--accent)`, `var(--space-4)` …). No hard-coded hex values in components.
8. **Tests with every feature.** `engine` is unit-tested; `render` has golden-frame tests (compare to reference images with a tolerance); `media` tests run on generated clips (see `docs/phase-0.md`).
9. **Benchmark before every commit that touches media, render or playback.** A change that worsens any budget number by more than 10% fails.
10. **Small commits**, each building and passing tests, with a message that says what changed and why.
11. **Log decisions.** Any non-obvious choice (a crate, a workaround, a fallback) gets a dated entry in `docs/decisions.md`.

## Performance budget

Measured on two floors: a Windows laptop running the app on Intel integrated graphics (Alex's MSI with the NVIDIA GPU switched off for the app), and an 8 GB M1 or M2 MacBook Air.

| Measure | Target |
| --- | --- |
| Download size | under 150 MB including FFmpeg |
| Memory, 10-minute 1080p project open | under 1.5 GB |
| Cold start to usable | under 3 s |
| Playback, 1080p H.264, 2 tracks + titles | 30 fps, no dropped frames |
| Playback, 4K HEVC phone footage | 30 fps from a proxy built in the background |
| Scrub: frame shown after a seek | under 100 ms |
| Export, 10 minutes of 1080p | under 5 minutes with a hardware encoder |
| Captions, 10 minutes of speech (Phase 2) | under 5 minutes, offline |

## Commands

Fill these in during Phase 0 and keep them current:

- Dev run: `…`
- Tests: `…`
- Lint: `cargo clippy --workspace -- -D warnings` and the UI linter
- Benchmark: `…`
- Release build: `…`

## Out of scope for version 1

Node-based compositing, 3D, camera RAW (Blackmagic RAW, RED, ARRI), HDR export, a full audio mixing suite, cloud or collaboration features, plugins, Linux, automatic face-tracking reframe. Do not start these without Alex asking.
