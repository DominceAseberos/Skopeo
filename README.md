# Skopeo

**Visual context for AI coding agents.**

Skopeo is a VS Code-compatible extension for inspecting a running local web app, annotating elements, drawing directly on captured screenshots, and generating structured visual context for AI coding agents.

> This public repository is the distribution, documentation, website, and issue-tracking home for Skopeo. The development source code is maintained privately.

## Download

- **Latest VSIX:** https://dominceaseberos.github.io/Skopeo/downloads/skopeo-ui-latest.vsix
- **Website:** https://dominceaseberos.github.io/Skopeo/
- **VS Code Marketplace:** search for `Domincee.skopeo-ui`

## Supported IDEs

- **Visual Studio Code** — direct Chat / Agent handoff when the supported VS Code command is available.
- **Antigravity** — direct agent-panel handoff through the supported Antigravity command.
- **Kiro** — user-tested compatible for VSIX installation, Activity Bar usage, localhost preview, annotations, and generated AI context.

Skopeo can also run in compatible desktop VS Code-based IDEs that provide the extension APIs it uses. If direct AI submission is unavailable, Skopeo can still generate context and copy the handoff instruction.

## What Skopeo does

- Opens a local web app through Skopeo's loopback-only preview bridge.
- Lets you hover, inspect, and annotate actual rendered elements.
- Captures useful HTML, computed CSS, selectors, text, viewport information, and screenshots.
- Lets you draw on screenshots with pen, arrows, rectangles, circles, text, eraser, undo, and clear tools.
- Generates `.ai-context/` plus `PROMPT.md` for an AI coding agent.
- Supports Send to AI and Copy Prompt workflows.
- Includes a no-backend GitHub bug-report form with optional screenshot/recording upload.
- Checks the public Skopeo version feed for newer manual VSIX builds.

## Manual installation

1. Download the latest VSIX from the link above.
2. In VS Code, open **Extensions**.
3. Select the `...` menu.
4. Choose **Install from VSIX...**.
5. Select `skopeo-ui-latest.vsix`.
6. Reload the IDE if prompted.

For Marketplace installs, VS Code can use its normal extension update system. For manual VSIX installs and compatible IDEs such as Kiro or Antigravity, Skopeo includes a lightweight update check and a **Skopeo: Check for Updates** command.

## Bug reports

Use **Skopeo: Report a Bug** from the Command Palette or open an issue in this repository. The structured bug form accepts reproduction steps, environment information, logs, and optional screenshots or recordings.

## Public repository contents

This repository intentionally contains only public distribution material such as:

- the GitHub Pages landing site
- packaged VSIX builds
- changelog and product documentation
- public icons/assets
- issue templates

The TypeScript source, tests, build scripts, and development configuration are not stored in this public repository.

## Copyright

Copyright © DominceAseberos. All rights reserved unless a specific file states otherwise.
