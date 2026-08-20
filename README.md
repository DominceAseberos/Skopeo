# Skopeo

![Stable](https://img.shields.io/badge/stable-0.1.28-2563eb)
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

## Skopeo 0.1.28

The current stable release strengthens cross-IDE handoff and keeps visual intent intact from browser annotation to coding agent:

- Kiro is now a first-class host adapter with live command discovery, first-party activation, update recovery, and a non-blocking text submission path;
- Kiro image handoff can attach real Skopeo screenshots, Draw markup, and pasted visual references through the verified image-capable agent path;
- normal incremental **Send** now carries source ownership, Draw actions, placed-component intent, marked-up/original screenshots, and pasted references instead of reducing a visual change to text-only context;
- visual attachments are validated and fairly budgeted across annotations, prioritizing marked-up intent with a bounded 10-image / 12 MB direct-handoff budget;
- users can disable direct image attachments with `visualAIBridge.attachImagesToAgent`; a user-level `false` is authoritative and cannot be re-enabled by workspace settings, while text/source/visual-spec context remains available;
- Angular development source resolution now understands template files, owning TypeScript components, styles, common template event handlers, and generated `.angular` cache exclusions;
- real Chromium smoke tests now exercise Skopeo's actual screenshot and Canvas2D Draw pipeline, and CI validates Windows/Linux quality plus isolated release packaging.

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

Skopeo targets desktop **Visual Studio Code 1.90+** and compatible VS Code-based IDEs that expose the extension APIs Skopeo uses. Antigravity and Kiro are known compatible environments.

The browser workflow accepts local HTTP development targets such as:

```text
http://localhost:3000
http://127.0.0.1:5173
```

Current source-resolution coverage is strongest for React/Vite and Next.js development mode. Angular development projects also resolve template/component/style ownership when supported evidence is available, with bounded structural fallback for additional web stacks.

The current stable package bundles native graph enrichment for **Windows x64**. Other desktop/remote extension-host platforms continue to use supported exact runtime provenance and heuristic source resolution when the native graph helper is unavailable; relationship enrichment may be reduced on those platforms.

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

Current stable: **0.1.28**

See `CHANGELOG.md` for public release notes.

## License

MIT
