# Changelog

## Unreleased

- Added a dedicated **Copy Prompt** button beside **Send to AI**. It generates the current `.ai-context/` and copies the standard handoff instruction to the clipboard without changing the configured agent mode.
- Added a floating Skopeo toolbar inside the real browser preview with **Annotate**, **Send**, **Copy**, and **Clear** actions. The toolbar is excluded from element selection and viewport screenshots.
- Added automatic browser route synchronization for `pushState`, `replaceState`, back/forward navigation, and hash changes so generated `context.json` and `PROMPT.md` use the actual current route.
- Added an active **Notes** list to the floating browser toolbar with inline annotation-instruction editing and saving back to the shared IDE/context session.
- Added drag-to-move positioning for the floating toolbar with viewport clamping, plus an icon-only minimized mode that can be expanded again in place.
- Added browser-side confirmation popups for successful annotation saves/updates, AI handoff or prompt copy, and session clearing, plus error feedback for failed actions.
- Added screenshot thumbnails and a layered **Draw** editor inside Notes with pen, arrow, rectangle, circle, text, eraser, undo, and clear tools. Saved drawings keep editable normalized vector actions, preserve the original screenshot, and generate a separate marked-up PNG for AI context.
- Added pasted visual-reference layers in Draw. `Ctrl+V` / `Cmd+V` accepts multiple clipboard images per annotation with drag/resize, duplicate/delete, opacity, Fit, Original Size, crop, ±90° rotation, and shared forward/backward ordering.
- Added a reusable component-placement engine with multiple components per annotation, drag/resize, editable props/variants, rotation, duplicate/delete, and shared stacking with pasted reference images.
- Added a searchable **Components** palette with an offline Skopeo Core library plus optional shadcn/ui catalog metadata fetched through the extension host. Components can be clicked to add or dragged to an exact position; third-party React/TSX source is never executed in the inspected page.
- Expanded `.ai-context/` visual specifications: pasted references are written as separate `annotation-NNN-reference-NNN.png` files, marked-up screenshots flatten references/components/drawings, and `context.json` / `PROMPT.md` preserve annotation association, provider/component identity, source reference, props, normalized geometry, crop/opacity/rotation, and shared z-order.
- Fixed wheel/trackpad scroll leaking through the floating Notes UI into the preview page. Hovered Skopeo textareas/panels now consume local scrolling and block page scroll chaining at their edges.
- Fixed an early-page-load crash when the floating browser toolbar was injected before `document.body` existed; the toolbar now waits for DOM readiness without aborting annotation runtime startup.
- Added a **Report a Bug** action in both the Skopeo panel and Command Palette. It opens the repository's structured GitHub Issue Form with the Skopeo version, IDE, OS, and architecture prefilled, plus fields for reproduction steps, logs, and optional screenshot/recording uploads.
- Added a no-backend update checker for manual VSIX installs. Skopeo checks the public GitHub Pages `latest.json` at most once per day, offers the latest direct VSIX or Marketplace when a newer stable version exists, and exposes **Skopeo: Check for Updates** for an immediate manual check. The background check can be disabled with `visualAIBridge.checkForUpdates`.
- Documented Kiro as a user-tested compatible VS Code-based IDE while keeping dedicated direct-agent claims limited to the integrations Skopeo actually detects.
- Expanded automated coverage to **135 passing tests across 26 files**, including browser-preview controls, visual-spec validation, pasted-image transforms, component placement/rendering, cross-layer ordering, component providers/catalog transport, shadcn registry parsing, AI-context serialization, route synchronization, bug-report URL generation, and update version comparison.
- Documentation and compatibility follow-up work after the 0.1.13 baseline.

## 0.1.13

- Added Automatic Local Proxy Injection on a loopback-only server using bundled `http-proxy-3`.
- Added automatic runtime injection into HTML while preserving the requested localhost path, query, and hash.
- Added HTML transformation for identity, gzip, deflate, and Brotli responses.
- Added same-origin redirect rewriting and forwarding for ordinary development WebSockets, including Vite HMR.
- Added an authenticated reserved bridge WebSocket using a fresh random token per configured target and exact `Origin` validation.
- Kept manual `/runtime.js` injection as a fallback for origin-sensitive applications and strict CSP development setups.
- Added visible strict-CSP failure diagnostics rather than weakening the target application's policy.
- Replaced selected-element screenshot rendering with `html2canvas` viewport capture so annotations retain surrounding page context and numbered-pin location.
- Added complete browser runtime shutdown for sockets, reconnect timers, event listeners, animation frames, overlays, pins, and the comment editor.
- Added the Stop command and server lifecycle handling.
- Added a per-workspace lock for context generation and clearing.
- Added rollback-safe `.ai-context/` installation using temporary and backup directories.
- Added recovery for orphaned backups plus Windows-safe rename retries for `EPERM`, `EBUSY`, `EACCES`, and `ENOTEMPTY`.
- Added terminal-result UI recovery for generated, error, cleared, clear-error, agent-error, stopped, and unexpected extension failures.
- Expanded AI handoff to four modes: `chat`, `clipboard`, `generateOnly`, and `terminal`, with `chat` as the default.
- Added direct IDE handoff through an optional configured chat command, Antigravity Agent, and VS Code Chat/Agent when those commands are registered.
- Added safe chat fallback: when direct IDE submission is unavailable or fails, generated context remains valid and the short handoff instruction is copied to the clipboard.
- Added network-level compatibility coverage for Astro/Vite, React/Vite, Next.js development patterns, a non-Vite server, Vite HMR, manual fallback, strict CSP, compressed HTML, and canonical URL preservation.
- Added an esbuild metadata audit that rejects unexpected external dependencies from the packaged extension bundle.
- Updated the automated validation baseline to **75 passing tests across 12 files**.
- Updated project documentation to describe the real-browser proxy architecture instead of the retired iframe-first/manual-script-first plan.

## 0.1.0

Initial local-only MVP:

- Added the Visual AI Bridge Activity Bar webview and start command.
- Added a loopback-only HTTP server for `/health` and the bundled `/runtime.js`.
- Added origin-, source-, session-, type-, and size-validated runtime messaging.
- Added hover inspection, click interception, comment editing, numbered pins, deletion, and pin restoration after reload.
- Added stale-selector reporting after page reloads.
- Added stable selector generation with attribute, class, and `:nth-of-type()` fallbacks.
- Added sanitized DOM, computed CSS, text, element rectangle, viewport, and screenshot extraction.
- Added privacy redaction for form values, editable text, scripts, styles, inline handlers, and bridge UI.
- Added atomic `.ai-context/` generation with rollback-safe replacement.
- Added conditional screenshot references and non-blocking screenshot warnings.
- Added `.gitignore` handling with line-ending preservation.
- Added generate-only, clipboard, and visible-terminal agent modes with shell-safe path substitution.
- Added strict webview CSP, loopback URL validation, payload limits, and PNG validation.
- Added unit and integration-level tests for extraction, protocols, validation, context generation, `.gitignore`, command templates, and the local HTTP server.
- Added reproducible VSIX packaging with locally installed `@vscode/vsce`.
