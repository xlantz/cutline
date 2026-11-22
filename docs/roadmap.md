# Roadmap and version 1 scope

The full plan, with diagrams, is Alex's "Lightweight Video Editor: Plan and Architecture" doc. This is the short version for the repo.

## Phases

Each phase ends in something usable and must pass its gate, plus the performance budget in `CLAUDE.md`, on both Windows and macOS.

| Phase | What gets built | Gate to move on |
| --- | --- | --- |
| 0. Proof of speed | Hardware decode, native viewer, playback, benchmark | 1080p plays at 30 fps on both test machines |
| 1. Cut | Import (drag and drop), timeline, split, trim, ripple delete, auto-proxies, autosave, undo, H.264 export | Edit and export a 10-minute video |
| 2. Talk | Offline transcript, text-based editing, silence and filler removal, captions, 9:16 and 1:1 projects, horizontal-to-vertical reframe, first-run tour | A first-time user makes a captioned short in under 10 minutes |
| 3. Layers | Unlimited tracks, titles, transitions, keyframes, audio mixing and ducking, contextual tips | 4 tracks + titles play at 30 fps |
| 4. Color | Correction sliders, curves, .cube LUTs, scopes, blur, vignette, chroma key | Graded 1080p plays in real time |
| 5. Ship | Windows and macOS installers, updater, crash recovery, export presets, Learn panel, shortcut presets | 4 of 5 first-time testers finish the tour and export a video unaided |

## Formats

- Open: MP4, MOV, MKV, WebM, AVI, M4V; H.264, HEVC, AV1, VP9, ProRes, DNxHR; phone HDR (converted to SDR); WAV, MP3, AAC/M4A, FLAC, Ogg; PNG, JPG, WebP, HEIC; SRT.
- Later: camera RAW, HDR export.

## Export presets

Stored in a data file that can update without a new release. Destinations: YouTube, YouTube Shorts, Instagram Reels, TikTok, Website, Master copy. Several can be exported at once. If the project's shape doesn't match the destination, offer to make the vertical (or horizontal) version first.

## Screens

Designs are on the "Cutline Editor Screens" canvas: edit workspace, vertical and captions, color, export, first-run tour, Learn panel. Visual rules are in the "Cutline Design System"; `design/tokens.css` is its code form.
