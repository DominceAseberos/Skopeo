# Skopeo

![Stable](https://img.shields.io/badge/stable-0.1.29-2563eb)
[![VS Code Marketplace](https://img.shields.io/badge/VS%20Code-Marketplace-007ACC?logo=visualstudiocode&logoColor=white)](https://marketplace.visualstudio.com/items?itemName=Domincee.skopeo-ui)
[![Open VSX](https://img.shields.io/badge/Open%20VSX-Registry-6B46C1)](https://open-vsx.org/extension/Domincee/skopeo-ui)
![Local First](https://img.shields.io/badge/local-first-16a34a)
![License](https://img.shields.io/badge/license-MIT-64748b)

**Skopeo connects the UI you are looking at to the source code and visual intent behind it.**

Open your real local web app, point at the exact element you want changed, describe the result directly on the page, inspect the source that owns it, and send structured context to the coding agent you already use.

## Why Skopeo

Screenshots show what a page looks like, but they usually do not tell a coding agent which rendered element you mean, which file owns it, what styles/state are related, or where a requested change should be applied.

Skopeo adds that missing layer:

**See it → Point at it → Understand its source → Explain it → Send it.**

## Skopeo 0.1.29

The current stable release hardens Skopeo for Windows x64 across fresh installs, IDE restarts, port conflicts, and difficult source-resolution cases:

- the packaged stable VSIX is independently verified on a fresh GitHub-hosted Windows VM, including install/activation, a workspace path with spaces and Unicode, sidebar close/reopen, Start → Stop → Start, runtime-port collision recovery, full IDE relaunch, real Chromium screenshot/Draw smoke, and native Codebase Memory startup/index/query;
- Kiro 0.12.333 and Antigravity IDE 1.107.0 pass packaged-VSIX clean-profile validation on Windows, including restart recovery, dynamic Source Intelligence, and host-scoped AI handoff; Kiro also keeps the verified direct image-attachment route, while Antigravity does not claim image pixels are attached when its current host API is text-only;
- Source Intelligence now rebinds to the selected project after preview reconnects, sidebar recreation, and project-scope changes, and can resolve classless dynamic React children when strong runtime-component and ancestor-structure evidence agree instead of requiring rendered text to exist literally in source;
- the local preview bridge automatically moves to another loopback port when the configured runtime port is occupied, with a bounded sequential search and OS-assigned fallback;
- the Windows x64 native Codebase Memory host is selected through an integrity manifest and verified before execution; cache/state stays outside the inspected repository and source resolution continues safely when graph enrichment is unavailable;
- macOS, Linux, ARM64, WSL/SSH/Dev Containers, and other remote extension-host environments are not claimed as release-verified in 0.1.29.

## Core workflow

1. Install Skopeo from the VS Code Marketplace, Open VSX, or the stable VSIX.
2. Open your web project and start its local development server.
3. Open the **Skopeo** Activity Bar view.
4. Enter your localhost URL and open the preview through Skopeo.
5. Turn on **Annotate** and click the UI you want changed.
6. Write the instruction and use **Draw** when visual markup is more useful than text.
7. Inspect **Source** to see the owning file/component and related context when available.
8. Use **Send** to generate and hand off the structured context to your coding workflow.

## What Skopeo captures

Depending on the selected element and framework support, an annotation can include:

- a stable selector and visible element evidence;
- sanitized markup and computed style context;
- viewport geometry and screenshot evidence;
- text instructions and Draw specifications;
- exact or high-confidence source ownership;
- owning component/function;
- related styles, state/hooks, behavior, and source files.

Skopeo keeps uncertainty explicit. If it cannot prove an exact source location, it reports a lower-confidence or unresolved state rather than presenting a guess as exact.

## Draw and visual specification

Draw supports freehand markup, arrows, rectangles, circles, text, pasted image references, layer ordering, and managed component previews.

Skopeo includes managed preview libraries for shadcn/ui and Mantine. These libraries are used to communicate visual intent; managing them in Skopeo does not install those libraries into the inspected application.

## IDE and framework support

Skopeo 0.1.29 is release-verified for **Windows x64**. The packaged VSIX is independently validated on a clean Visual Studio Code Windows VM, and clean-profile Windows validation also covers Kiro 0.12.333 and Antigravity IDE 1.107.0. Other operating systems and remote extension-host environments are intentionally not advertised as release-verified yet.

The browser workflow accepts local HTTP development targets such as:

```text
http://localhost:3000
http://127.0.0.1:5173
```

Current source-resolution coverage is strongest for React/Vite and Next.js development mode. Angular development projects also resolve template/component/style ownership when supported evidence is available, with bounded structural fallback for additional web stacks.

The current stable package bundles verified native graph enrichment for **Windows x64**. If the native host is missing, fails integrity validation, or cannot start, Skopeo keeps exact runtime provenance and bounded heuristic source resolution available instead of making Codebase Memory a single point of failure. macOS/Linux/ARM64/remote-host behavior remains unverified for this release.

## Privacy and security

Skopeo is designed for local development:

- preview targets are restricted to loopback HTTP hosts;
- the runtime proxy binds to the local loopback interface, rejects unexpected Host headers, and only trusts an IDE-resolved forwarded origin when one is explicitly provided;
- browser runtime connections use session authentication/origin checks;
- generated context is written locally to the selected project, with recognized token/secret-like URL parameters and DOM attributes redacted from text context;
- captured application content is marked as untrusted evidence for AI handoff so text displayed by the inspected page is not treated as coding-agent instruction authority;
- workspace-controlled terminal/custom-command handoffs are restricted in untrusted VS Code workspaces;
- source navigation rejects paths that escape the selected project/workspace;
- Code Intelligence cache/state is stored outside the inspected repository;
- the normal workflow does not require a Skopeo account or hosted Skopeo backend.

Generated `.ai-context/` can contain your own annotations, screenshots, and development context. Screenshot pixels are not secret-redacted automatically; review visible data before sending, or disable direct image attachments in Skopeo settings when needed.

## Install

### VS Code Marketplace

Search for **Skopeo** in Extensions, or use the Marketplace badge at the top of this page.

### Open VSX

For compatible VS Code-based IDEs that use the Open VSX Registry, install **Skopeo** from the Open VSX badge at the top of this page.

### Stable VSIX

The public Skopeo site provides `skopeo-ui-latest.vsix` for manual installation in VS Code-compatible IDEs.

In VS Code: **Extensions → … → Install from VSIX…**

Registry installs use the IDE's normal extension update mechanism. Manual VSIX installs can use **Skopeo: Check for Updates** against the stable direct-download channel. Registry availability can lag the direct GitHub stable VSIX.

## Documentation inside VS Code

The README shown on the Skopeo extension page in VS Code is an end-user manual with the preview controls, Source / Changes behavior, Draw workflow, shortcuts, settings, security notes, and troubleshooting steps.

## Support

Use the GitHub issue templates in this repository for reproducible bugs. Include your IDE/version, framework/dev-server setup, what you expected, what happened, and the smallest safe reproduction details you can share.

## Stable release

Current stable: **0.1.29**

See `CHANGELOG.md` for public release notes.

## License

MIT
