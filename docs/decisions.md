# Decisions log

Newest first. One entry per non-obvious choice: date, decision, why, and what we'd do if it turns out wrong.

## 2026-11-07: Tauri 2 shell with a native GPU viewer

- **Decision:** Tauri 2 with a React interface; the video viewer is a native GPU surface laid over the web view.
- **Why:** small download and low idle memory; the designs are web pages so the UI ports directly; the native viewer avoids copying frames into the web page.
- **If wrong:** fully native interface (Slint or egui), decided at Phase 0 Checkpoint B.

## 2026-11-07: LGPL FFmpeg from day one

- **Decision:** ship only an LGPL FFmpeg build and export through hardware encoders.
- **Why:** Cutline starts as a personal tool but may be sold; a GPL build (x264, x265) would force the whole app to be GPL.
- **If wrong:** if hardware encoders prove unreliable, consider a commercially licensed x264 or open-sourcing under GPL.

## 2026-11-07: Windows and macOS from Phase 0

- **Decision:** build and test both platforms from the first phase.
- **Why:** adding a second platform late costs far more than building on two from the start.
