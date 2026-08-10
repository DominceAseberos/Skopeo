# Changelog

## Unreleased

## 0.1.23 - 2026-08-10

- Reworked managed component-library controls into compact icon-only **Available** and **Installed** tabs. Installed libraries are removed from the Available view, successful installs switch directly to Installed, and a warning badge appears on the relevant tab when an install fails or an installed library has an update available.
- Added **Mantine** as Skopeo's second managed real-component library with **20 component families / 61 presets** built from `@mantine/core`. Mantine runs in its own provider-scoped sandbox renderer and global-storage installation, coexists with shadcn/ui, supports live Interact mode and renderer capture, and never installs packages into the inspected project.
- Mantine's first managed install can promote the trusted renderer bundled in the VSIX into extension global storage, while later update/repair operations retain the verified Skopeo package flow. Appearance reset wording is now provider-neutral.

## 0.1.22 - 2026-08-10

- Added live **Interact mode** for real renderer-backed components in Draw. Edit mode keeps drag/resize/rotate/layer controls; Interact mode gives the sandboxed component frame pointer and keyboard input for hover, click, typing, tabs, accordions, dropdowns, tooltips, sliders, carousels, and internal scrolling.
- Real shadcn preset previews in the Components browser are now interactive before placement. The preview surface receives component input while dragging remains available from the surrounding preset card/footer.
- Added an explicit **Edit / Interact** toggle to the placed-component inspector, a cyan live-interaction outline/badge, and `Esc` handling across the sandbox boundary to return to Edit mode.
- Interaction state is preview-only by default. **Save Drawing** exits Interact mode and recreates the component from saved props before renderer capture, so temporary clicks, opened accordions, and typed test values do not silently become AI implementation intent.
- Upgraded the source renderer to **0.1.3** with iframe overscroll containment and the narrow `skopeo-renderer:exit-interact` message used by `Esc`.

## 0.1.21 - 2026-08-09

- Upgraded the component preview renderer to **0.1.2**. Real component frames now use a strict viewport-sized centered surface so presets remain centered inside the preview canvas.
- Fixed shadcn/ui **Sidebar** presets rendering as blank white frames. The preview composition no longer depends on a missing `TooltipProvider`, and Sidebar uses a preview-safe static shadcn composition so narrow preview iframes cannot hide it behind responsive mobile/off-canvas behavior.
- Verified Button, Avatar, and Sidebar with real headless Chrome rendering at a narrow 450×320 preview viewport.

## 0.1.20 - 2026-08-09

- Added a dedicated **Appearance** workflow for placed UI components in Draw. Selected components now expose preview contrast modes plus explicit **Background**, **Text**, and **Border** color controls.
- Added **Preview Contrast** modes: Original, Auto, Light backdrop, Dark backdrop, and Outline. These are editor-only visibility aids and are intentionally stripped from generated AI implementation context.
- Added screenshot-aware **Auto Contrast**. When explicitly triggered, Skopeo samples the underlying annotation screenshot and applies real high-contrast component colors; these deliberate colors are preserved as implementation intent.
- Added **Reset shadcn** to clear explicit appearance overrides and return the component to its original preset/theme styling.
- Explicit appearance overrides now propagate through the real sandboxed renderer, flattened annotation screenshots, saved visual specifications, `context.json`, and `PROMPT.md`. The local DOM/canvas fallback honors the same appearance values.
- Upgraded the bundled and managed **Skopeo Component Renderer to 0.1.1** while preserving **61 real component families / 365 presets**. If an older managed renderer such as 0.1.0 is still installed, Skopeo now prefers the newer bundled renderer immediately while keeping the normal managed-library **Update** action available.
- Regenerated the versioned managed-renderer package and SHA-256 metadata for renderer 0.1.1.
- Expanded automated coverage to **167 passing tests across 35 files**, including appearance validation, preview-only context separation, inspector controls, renderer URL propagation, fallback rendering, Auto Contrast behavior, reset behavior, and protection against an older managed renderer overriding a newer bundled renderer.
- Updated the public website and release documentation for the component appearance/contrast workflow.

## 0.1.19 - 2026-08-09

- Added a dedicated **Copy Prompt** button beside **Send to AI**. It generates the current `.ai-context/` and copies the standard handoff instruction to the clipboard without changing the configured agent mode.
- Added a floating Skopeo toolbar inside the real browser preview with **Annotate**, **Send**, **Copy**, and **Clear** actions. The toolbar is excluded from element selection and viewport screenshots.
- Added automatic browser route synchronization for `pushState`, `replaceState`, back/forward navigation, and hash changes so generated `context.json` and `PROMPT.md` use the actual current route.
- Added an active **Notes** list to the floating browser toolbar with inline annotation-instruction editing and saving back to the shared IDE/context session.
- Added drag-to-move positioning for the floating toolbar with viewport clamping, plus an icon-only minimized mode that can be expanded again in place.
- Added browser-side confirmation popups for successful annotation saves/updates, AI handoff or prompt copy, and session clearing, plus error feedback for failed actions.
- Added screenshot thumbnails and a layered **Draw** editor inside Notes with pen, arrow, rectangle, circle, text, eraser, undo, and clear tools. Saved drawings keep editable normalized vector actions, preserve the original screenshot, and generate a separate marked-up PNG for AI context.
- Added pasted visual-reference layers in Draw. `Ctrl+V` / `Cmd+V` accepts multiple clipboard images per annotation with drag/resize, duplicate/delete, opacity, Fit, Original Size, crop, ±90° rotation, and shared forward/backward ordering.
- Added a reusable component-placement engine with multiple components per annotation, drag/resize, editable props/variants, rotation, duplicate/delete, and shared stacking with pasted reference images.
- Added a searchable **Components** side panel with offline Skopeo Core components plus the real shadcn renderer catalog, using a managed extension-storage library when installed and the bundled renderer as fallback. The UX is now **Provider → Category → Component family → Preset/variant** rather than one flat component list; clicking Button, for example, exposes its full curated preset set.
- Added the separate **Skopeo Component Renderer** project. It owns the actual installed React/shadcn source, generates a manifest from the installed UI files, currently covers **61 real component families / 365 presets**, and builds an isolated local renderer application. Skopeo now also produces a versioned prebuilt renderer-library artifact and can install it into extension global storage, shared across workspaces, without modifying the user's project.
- Added **Install Library / Update / Repair / Remove** controls for shadcn/ui previews. Downloads are restricted to the Skopeo public artifact path, package and per-file SHA-256 hashes are verified, unsafe/duplicate paths are rejected, installation is staged and manifest-validated, and activation is atomic with rollback. The active catalog and loopback renderer root hot-swap immediately; removing the managed copy falls back to the renderer bundled with Skopeo.
- Real preset previews and placed components now render in sandboxed `allow-scripts` frames without `allow-same-origin`. Renderer frames are served only from the Skopeo loopback bridge under `/__skopeo_components/`, cannot access the inspected application's DOM, and communicate through a narrow `postMessage` ready/capture contract.
- Saving visual markup now captures the exact renderer frame to a transparent PNG and places that visual into the flattened annotation screenshot. If a renderer capture is unavailable, Skopeo falls back to its local DOM/canvas adapter rather than failing the save.
- Expanded `.ai-context/` visual specifications: pasted references are written as separate `annotation-NNN-reference-NNN.png` files, marked-up screenshots flatten references/components/drawings, and `context.json` / `PROMPT.md` preserve annotation association, provider/component identity, source reference, props, normalized geometry, crop/opacity/rotation, and shared z-order.
- Fixed wheel/trackpad scroll leaking through Skopeo UI into the preview page. Notes textareas/panels and the Draw stage, component palette, and inspectors now share local scroll containment and block page scroll chaining at their edges.
- Fixed an early-page-load crash when the floating browser toolbar was injected before `document.body` existed; the toolbar now waits for DOM readiness without aborting annotation runtime startup.
- Added a **Report a Bug** action in both the Skopeo panel and Command Palette. It opens the repository's structured GitHub Issue Form with the Skopeo version, IDE, OS, and architecture prefilled, plus fields for reproduction steps, logs, and optional screenshot/recording uploads.
- Added a no-backend update checker for manual VSIX installs. Skopeo checks the public GitHub Pages `latest.json` at most once per day, offers the latest direct VSIX or Marketplace when a newer stable version exists, and exposes **Skopeo: Check for Updates** for an immediate manual check. The background check can be disabled with `visualAIBridge.checkForUpdates`.
- Documented Kiro as a user-tested compatible VS Code-based IDE while keeping dedicated direct-agent claims limited to the integrations Skopeo actually detects.
- Expanded automated coverage to **158 passing tests across 33 files**, including browser-preview controls, Draw side-panel layout/scroll containment, visual-spec validation, pasted-image transforms, renderer-manifest parsing, local manifest precedence, sandbox frame/capture messaging, loopback renderer asset serving, family→preset palette navigation, exact renderer-preset placement, renderer-aware protocol/context serialization, cross-layer ordering, route synchronization, bug-report URL generation, and update version comparison.
- Hardened VSIX packaging so local log/audit artifacts are excluded from release bundles; the 0.1.19 package follows the canonical check → test → build → renderer-package → VSIX → site-sync pipeline.
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
