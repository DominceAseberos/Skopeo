# Changelog

Public release notes for Skopeo. Detailed engineering history is maintained in the private source repository.

## Unreleased

## 0.1.27 - 2026-08-19

- Hardened the localhost preview bridge against DNS-rebinding-style host confusion while preserving explicitly resolved remote/forwarded IDE origins and WebSocket connectivity.
- Added bounded decompression for proxied HTML responses and serialized screenshot capture so malformed compression or repeated timed-out captures cannot stack unbounded extension-host work.
- Added defense-in-depth redaction for sensitive DOM attributes and URL parameters before `.ai-context` is written, and marked captured page content as untrusted evidence in AI handoffs to reduce prompt-injection risk.
- Added VS Code Restricted Mode protections that prevent workspace-controlled terminal/custom-agent execution until the workspace is trusted and safely fall back to clipboard handoff when needed.
- Strengthened stable release isolation so non-stable internal collaboration/MCP markers are physically absent from compiled stable bundles and audited before packaging.
- Reworked stable packaging to use an isolated staging workspace instead of temporarily mutating source manifests or renderer state; the packaged VSIX now also includes the license explicitly.
- Resolved the remaining development dependency advisory and documented the current Windows-x64 native graph-enrichment boundary with graceful source-resolution fallback on other supported environments.

## 0.1.26 - 2026-08-19

- Hardened Skopeo controls inside dynamically opened modals, dialogs, drawers, sheets, and custom vanilla overlays so using Notes, Source / Changes, shortcuts, and other Skopeo UI does not accidentally trigger common outside-click or focus-dismiss behavior.
- Added class-independent modal inference, nested-modal switching, Shadow DOM traversal, closing-state cleanup, shared overlay observation, and safer top-layer portal handling.
- Improved screenshot capture stability by waiting for fonts/images, detecting layout shifts, retrying unstable frames, and bounding high-DPI / ultrawide capture memory.
- Improved screenshot fidelity for local development assets, origin-clean canvases, and visible video frames while keeping Skopeo-owned UI out of captures.
- Improved preview opening for VS Code-compatible remote/forwarded environments by resolving external preview URIs and preserving the forwarded WebSocket origin.

## 0.1.25 - 2026-08-18

- Added local runtime-to-source intelligence so supported React/Vite and Next.js development annotations can resolve exact files, lines, columns, owning components/functions, and confidence states.
- Added Source Structure ownership for markup, styles, state/hooks, behavior, related files, and structural graph relationships, with safe fallback states when exact provenance is unavailable.
- Added exact source navigation through the current VS Code-compatible IDE instead of requiring users or agents to manually search for the owning file.
- Redesigned the browser preview around a compact five-action toolbar, a readable left annotation rail, contextual Notes beside the selected target, a draggable Source / Changes inspector, and a bottom-right hide/show control.
- Added collision-aware overlay placement so Skopeo panels avoid covering each other when another side of the target has room.
- Improved Source / Changes readability and file click targets, moved transient messages to bottom-center, and kept Skopeo controls usable inside native browser dialogs.
- Added customizable preview shortcuts with a compact multi-column key picker, duplicate-key prevention, and immediate runtime updates.
- Fixed long annotation text so it wraps vertically, grows to a bounded height, and never requires horizontal scrolling.
- Hardened Code Intelligence indexing, cache isolation, workspace realpath/symlink handling, refresh scheduling, parsing, and process integration while keeping generated index state outside the inspected repository.

## 0.1.24 - 2026-08-11

- Promoted the modern compact sidebar, browser annotation controls, responsive Draw workspace, Layers / Added Items, pasted references, and improved interaction/focus behavior.
- Added the current shadcn/ui and Mantine component-preview workflow, separate Components / Libraries workspaces, library maintenance states, real component interaction, and appearance/contrast controls.
- Added explicit release packaging and public distribution checks for the stable product line.

## 0.1.23 - 2026-08-10

- Added Mantine as a second managed real-component preview library alongside shadcn/ui.
- Added separate Available / Installed library management and update/error indicators.
- Improved multi-provider component preview and appearance controls.

## 0.1.22 - 2026-08-10

- Added Edit / Interact modes for supported real component previews.
- Made component-browser previews interactive before placement.
- Added keyboard return-to-edit behavior and safer preview-state handling before Save Drawing.

## 0.1.21 - 2026-08-09

- Improved real component preview layout and narrow-frame behavior.
- Fixed blank Sidebar-style component previews.

## 0.1.20 - 2026-08-09

- Added placed-component appearance controls and preview contrast helpers.
- Added explicit background, text, and border styling intent for placed components.

## 0.1.19 - 2026-08-09

- Added Copy Prompt, browser-side Skopeo controls, active Notes, Draw, pasted visual references, and component placement.
- Added real shadcn/ui component previews and managed preview-library controls.
- Added browser route awareness, bug reporting, and manual VSIX update checks.

## 0.1.13 - 2026-08-08

- Added the real-browser local preview workflow, automatic annotation runtime loading, viewport screenshots, improved shutdown, safer context generation, and expanded agent handoff modes.

## 0.1.0

Initial Skopeo MVP with local browser annotation and AI-ready visual context generation.
