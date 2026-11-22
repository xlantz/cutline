# Start here (for Alex)

This folder is the starting point of the Cutline code repository. Claude Code reads `CLAUDE.md` automatically and works from `docs/phase-0.md`.

## One-time setup on your Windows PC

1. Install: [Rust](https://rustup.rs), [Node.js LTS](https://nodejs.org), [Git](https://git-scm.com), and Visual Studio Build Tools with the "Desktop development with C++" workload.
2. Create a private GitHub repository named `cutline` and clone it to your PC.1
3. Copy everything in this folder into the repository and commit it.
4. Open Claude Code in the repository folder.

## Your first message to Claude Code

> Read CLAUDE.md and docs/phase-0.md. Make a plan for tasks 0.1 to 0.6 and show it to me before writing code. Then build them one at a time, committing after each, and stop at Checkpoint A with a plain-language report.

## How each phase goes

1. Claude Code plans the phase; you approve it.
2. It builds in small commits, running tests and the benchmark.
3. You try the build: on the MSI with the app set to "Power saving" (Windows Settings > Display > Graphics), and the Mac build from GitHub on a Mac.
4. Bring notes back to the planning chat; adjust designs or scope before the next phase.

## Where everything lives

- Plan and diagrams: the "Lightweight Video Editor: Plan and Architecture" doc
- Screens: the "Cutline Editor Screens" canvas
- Visual rules: the "Cutline Design System" (`design/` here is its code form)
