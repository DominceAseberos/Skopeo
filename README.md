# Skopeo

![Stable](https://img.shields.io/badge/stable-0.1.27-2563eb)
[![VS Code Marketplace](https://img.shields.io/badge/VS%20Code-Marketplace-007ACC?logo=visualstudiocode&logoColor=white)](https://marketplace.visualstudio.com/items?itemName=Domincee.skopeo-ui)
![Local First](https://img.shields.io/badge/local-first-16a34a)
![License](https://img.shields.io/badge/license-MIT-64748b)

**Skopeo connects the UI you are looking at to the source code and visual intent behind it.**

Open your real local web app, point at the exact element you want changed, describe the result directly on the page, inspect the source that owns it, and send structured context to the coding agent you already use.

## Why Skopeo

Screenshots show what a page looks like, but they usually do not tell a coding agent which rendered element you mean, which file owns it, what styles/state are related, or where a requested change should be applied.

Skopeo adds that missing layer:

**See it → Point at it → Understand its source → Explain it → Send it.**

## Skopeo 0.1.27

The current stable release hardens the local browser-to-agent workflow without changing the core annotation experience:

- the localhost preview bridge now validates request hosts and forwarded IDE origins to reduce DNS-rebinding exposure while preserving remote/forwarded development workflows;
- compressed preview HTML is bounded after decompression so unusually small compressed responses cannot expand into unbounded extension-host memory use;
- sensitive token-, session-, password-, authorization-, and key-like DOM attributes and URL parameters are redacted before generated context is written;
- generated AI handoffs explicitly treat captured page content as untrusted evidence rather than instructions, reducing prompt-injection risk from inspected applications;
- VS Code Restricted Mode disables workspace-controlled terminal/custom-agent execution and falls back to safer handoff behavior until the workspace is trusted;
- screenshot captures are serialized so a timed-out renderer cannot accumulate overlapping full-page capture jobs;
- stable release bundles are now audited to prove non-stable internal feature paths are physically absent, and release packaging runs from an isolated staging workspace;
- native structural graph enrichment is currently bundled for Windows x64; other supported environments continue with exact framework provenance and bounded heuristic source resolution when the native graph host is unavailable.

## Core workflow

1. Install Skopeo from the Marketplace or the stable VSIX.
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

Current source-resolution coverage is strongest for React/Vite and Next.js development mode, with structural fallback support for additional web stacks.

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

Generated `.ai-context/` can contain your own annotations, screenshots, and development context. Review it before sharing or committing it when working with sensitive projects.

## Install

### VS Code Marketplace

Search for **Skopeo** in Extensions, or use the Marketplace badge at the top of this page.

### Stable VSIX

The public Skopeo site provides `skopeo-ui-latest.vsix` for manual installation in VS Code-compatible IDEs.

In VS Code: **Extensions → … → Install from VSIX…**

## Documentation inside VS Code

The README shown on the Skopeo extension page in VS Code is an end-user manual with the preview controls, Source / Changes behavior, Draw workflow, shortcuts, settings, security notes, and troubleshooting steps.

## Support

Use the GitHub issue templates in this repository for reproducible bugs. Include your IDE/version, framework/dev-server setup, what you expected, what happened, and the smallest safe reproduction details you can share.

## Stable release

Current stable: **0.1.27**

See `CHANGELOG.md` for public release notes.

## License

MIT
