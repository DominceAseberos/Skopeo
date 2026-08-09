# Skopeo (formerly Visual AI Bridge)

![Version](https://img.shields.io/badge/version-0.1.19-2563eb)
![TypeScript](https://img.shields.io/badge/TypeScript-strict-3178C6?logo=typescript&logoColor=white)
![Tests](https://img.shields.io/badge/tests-158%20passing-22c55e)
![Local only](https://img.shields.io/badge/data-local%20only-8b5cf6)
![License](https://img.shields.io/badge/license-MIT-64748b)

> **Demo:** _(Add a `media/demo.gif` here showing Skopeo in action!)_

**Skopeo is a local extension for VS Code and compatible VS Code-based IDEs that visually annotates elements in a real browser and generates structured UI context for an AI coding agent.**

It captures the selected element's DOM context, CSS selector, computed styles, viewport position, visible text, and a viewport screenshot containing a numbered pin. The extension writes the result to `.ai-context/` inside the current project.

No hosted backend, account, analytics service, paid API, CDN, or remote service is required.

## How it works

Skopeo uses **Automatic Local Proxy Injection**:

1. The extension starts one combined HTTP, proxy, runtime, and WebSocket server on `127.0.0.1`.
2. You enter a localhost development URL, including any path, query, and hash. The `http://` scheme may be omitted for loopback URLs.
3. The extension normalizes the input to HTTP, uses only the target origin for proxy/server forwarding, and opens the same path, query, and hash through its local proxy.
4. HTML responses receive an external runtime script with escaped data attributes for the original origin and a random per-target token.
5. Normal HTTP requests and development WebSockets, including Vite HMR, continue to the target development server.
6. The browser runtime sends annotations through the reserved authenticated bridge WebSocket.
7. **Send to AI** creates `.ai-context/` and, in the default `chat` mode, submits a short instruction directly to a configured or detected IDE agent. If the IDE command is missing or fails at execution time, the context remains valid and Skopeo copies the same handoff instruction to the clipboard instead of failing the send.

The original page URL stored with each annotation is reconstructed from the target origin plus the browser's current proxy pathname, query, and hash. Skopeo also tracks `pushState`, `replaceState`, back/forward navigation, and hash changes so the session-level preview URL follows client-side navigation and is written consistently to `context.json` and `PROMPT.md`.

## Five-minute quick start

1. Install Skopeo from the VS Code Marketplace or install the `.vsix` in desktop VS Code / a compatible VS Code-based IDE such as Antigravity or Kiro.
2. Open and trust the web project's local folder.
3. Start the project's HTTP development server.
4. Run **Visual AI Bridge: Start**.
5. Enter the localhost URL and select **Open in Browser**. Full routes such as `localhost:4321/admin/dashboard?tab=users#active` are supported and preserved.
6. Select **Annotate**, click an element, and enter the requested change.
7. Select **Send to AI** to generate `.ai-context/`. In `chat` mode Skopeo submits directly when a configured or detected IDE agent command works; otherwise it keeps the generated context and copies the same handoff instruction to the clipboard as a safe fallback.
8. Or select **Copy Prompt** to generate the same context and copy the handoff instruction for manual pasting without changing your configured agent mode.
9. The browser preview also exposes floating **Annotate**, **Notes**, **Send**, **Copy**, and **Clear** controls. **Notes** shows the active annotation list and lets you edit/save instructions without returning to the IDE panel. **Draw** supports freehand markup, pasted visual references with `Ctrl+V` / `Cmd+V`, and draggable UI components from Skopeo Core or the optional shadcn/ui catalog. Drag the **Skopeo ⠿** handle to reposition the toolbar, or minimize it to icon-only controls.
10. Use **Clear Session** when you want to remove the annotation batch and generated files.
11. Use **Visual AI Bridge: Stop** to dispose the browser runtime, sockets, timers, overlays, pins, and local server.

Automatic mode does not require editing the target project. Manual injection remains available for origin-sensitive applications or strict CSP development setups.

## Capabilities

### Browser annotation

- Floating Skopeo controls directly in the browser preview for **Annotate**, **Notes**, **Send**, **Copy**, and **Clear**, so the normal annotation workflow does not require returning to the IDE panel
- The floating toolbar can be dragged anywhere in the viewport and minimized to an icon-only mode, with its position clamped on-screen
- **Notes (N)** opens the active annotation list directly in the browser, where annotation instructions can be edited and saved back to the same shared session used by the IDE panel and generated context
- Each Notes card shows its annotation screenshot and provides **Draw**. The visual editor supports pen, arrow, rectangle, circle, text, eraser, undo, and clear while preserving editable normalized drawing actions
- Paste one or more screenshot/reference images directly into Draw with `Ctrl+V` / `Cmd+V`; pasted references remain editable and support drag, resize, duplicate/delete, opacity, Fit, Original Size, crop, ±90° rotation, and shared bring-forward/send-backward ordering
- **Components** opens as a responsive side panel beside the screenshot instead of covering the Draw frame. Browse **Provider → Category → Component family → Preset/variant**. The shadcn renderer library currently exposes **61 real component families and 365 presets**, including 20 Button presets. The library card shows whether Skopeo is using the bundled fallback or a managed installation and provides **Install Library**, **Update**, **Repair**, and **Remove** actions. Managed installs live in extension global storage and never write preview-library files into the current project. Click **Add** or drag the exact preset onto the screenshot; multiple components can coexist in one annotation and remain movable, resizable, duplicable, rotatable, reorderable, and prop-editable
- Real shadcn previews come from the separate **Skopeo Component Renderer** project, which owns the actual installed React/shadcn source and builds an isolated local renderer app. Skopeo can install a versioned prebuilt renderer package into extension global storage and hot-swap the catalog/loopback renderer without restarting the IDE; the renderer bundled in the VSIX remains an offline/migration fallback. Each preview runs in a sandboxed `allow-scripts` frame without `allow-same-origin`, so it cannot access the inspected application's DOM. The existing Skopeo DOM/canvas adapter remains only as a fallback when a renderer frame is unavailable
- Draw uses one shared layer order for pasted references and placed components, then renders drawing markup above them. When saving, Skopeo asks each real renderer frame for a transparent PNG snapshot and uses that same rendered visual in the flattened marked-up screenshot; failed frame capture safely falls back to the local adapter instead of failing the annotation save
- Browser success/error popups confirm annotation saves/updates, AI handoff or prompt copy, and session clearing
- Mouse-wheel/trackpad scrolling stays inside the hovered Skopeo Notes panel, textarea, Draw stage, component palette, or inspector. Scroll chaining into the underlying preview page is blocked even when an inner Draw scroller reaches its edge
- The floating controls, annotation editor, and popups are excluded from captured screenshots and from selectable page elements
- Automatic HTML injection through a dedicated same-machine proxy
- Relative URL and asset behavior preserved through the proxy
- Development WebSocket and Vite HMR forwarding
- Hover inspection overlay
- Click to annotate; `Alt` + click selects the closest eligible parent
- Comment editor and numbered pins
- Current visible-viewport screenshots containing the numbered pin and the selected element in its surrounding page layout
- Screenshot thumbnails and lightbox preview in the VS Code panel
- Pin restoration and stale-selector reporting after reload
- Automatic route tracking for History API navigation, back/forward navigation, and hash changes
- One active authenticated browser runtime per session

### Context generation

Skopeo captures:

- Page URL and title
- User instruction
- Stable CSS selector
- Sanitized opening tag and HTML context
- Visible text
- Relevant computed CSS
- Element rectangle and viewport data
- Optional PNG screenshot of the browser's current visible viewport at annotation time
- Annotation ID and sequence number
- Optional editable drawing actions
- Optional pasted-reference metadata: source file, normalized position/size, crop, opacity, rotation, and layer order
- Optional placed-component metadata: annotation association, provider, component ID/source reference, variant, props, normalized position/size, rotation, and layer order

Generation is serialized per workspace. Files are written to a temporary directory and atomically installed. Existing context is backed up and restored if installation fails. Transient Windows `EPERM`, `EBUSY`, `EACCES`, and `ENOTEMPTY` rename failures are retried.

### Agent modes

- **Chat** _(default)_ — create `.ai-context/`, try the optional configured IDE command first, then detected built-in integrations. If direct submission is unavailable or the advertised command throws, Skopeo safely copies the same handoff instruction to the clipboard and reports that fallback in the panel.
- **Clipboard** — create `.ai-context/` and copy the agent instruction to the clipboard.
- **Generate Only** — create `.ai-context/` without starting an agent.
- **Terminal** — run a configurable command in a visible integrated terminal.

### IDE agent compatibility

Skopeo detects the available agent command at runtime rather than assuming a single IDE:

- **Visual Studio Code Chat / Agent** — supported when `workbench.action.chat.open` is registered. Skopeo submits the query in agent mode with automatic submission.
- **Antigravity Agent** — supported when `antigravity.sendPromptToAgentPanel` is registered. Skopeo passes the same short workspace-relative instruction directly to the Antigravity agent panel.
- **Kiro** — user-tested as compatible with Skopeo's VSIX, Activity Bar panel, localhost browser workflow, annotations, and context generation. Kiro is not currently claimed as a dedicated direct-agent integration; if no compatible chat command is exposed, Skopeo safely falls back to the clipboard.
- You can set `visualAIBridge.chatCommand` to an IDE-specific command ID; Skopeo passes the short handoff instruction as its first argument.
- If no supported command is registered, or a registered command fails when invoked, context generation still completes and `chat` mode falls back to the clipboard instead of returning an agent error.

The agent receives only the short handoff instruction. The full annotation instructions, selectors, HTML context, CSS, and screenshot references stay in `.ai-context/PROMPT.md` and its supporting files.

## Supported environments

### Required

- Desktop Visual Studio Code `1.90.0` or newer, or a compatible desktop VS Code-based IDE that supports the extension APIs Skopeo uses; Antigravity and Kiro are known compatible environments
- A trusted local file workspace
- An HTTP development server on `localhost`, `127.0.0.1`, or `[::1]`
- A browser with WebSocket support

For direct one-click AI handoff, the IDE must expose one of the currently supported chat commands listed above. Antigravity is currently supported through its native agent-panel command.

HTTPS targets, remote production domains, mobile devices, and other computers are intentionally excluded by the loopback-only security model.

### Compatibility coverage

The automated network-level smoke suite starts real HTTP and WebSocket fixture servers and verifies:

- Astro/Vite-style HTML
- React/Vite-style HTML
- Next.js development-style HTML
- A non-Vite HTTP server
- Vite HMR WebSocket forwarding
- Manual-injection fallback
- Strict nonce-only CSP failure reporting
- Original path, query, and hash preservation
- Gzip HTML transformation
- Legacy `/runtime.js` compatibility

These checks validate the proxy protocol and development-server patterns. Hands-on browser testing remains recommended for applications with custom authentication, cookies, service workers, OAuth callbacks, or other origin-sensitive behavior.

## Automatic mode and origin behavior

The browser-visible automatic URL uses the Visual AI Bridge proxy origin. Most applications based on relative URLs work normally, but origin-sensitive behavior can differ because browser storage and security boundaries use the proxy origin.

Potentially affected features include:

- `localStorage`, IndexedDB, and service-worker registrations
- OAuth redirect URI checks
- absolute-origin API allowlists
- cookies with explicit Domain or security policies
- application logic that compares `window.location.origin`

Use manual-injection mode when the application must keep its original browser origin.

## Manual-injection fallback

The legacy route remains supported:

```html
<script src="http://127.0.0.1:43119/runtime.js" data-visual-ai-bridge></script>
```

For a nonce-only CSP, add the same development nonce your application authorizes:

```html
<script
  nonce="YOUR_DEVELOPMENT_NONCE"
  src="http://127.0.0.1:43119/runtime.js"
  data-visual-ai-bridge
></script>
```

The development CSP must also allow the bridge WebSocket in `connect-src`, for example `ws://127.0.0.1:43119`.

Manual fallback workflow:

1. Add the script only to the development build.
2. Enter the original target URL in the Visual AI Bridge panel and select **Open in Browser** once so the bridge configures that exact target origin and creates a fresh token.
3. If automatic injection displays the CSP diagnostic page, open the original target URL directly.
4. The target page loads `/runtime.js` from the bridge; the server supplies the token from the external script response.
5. Remove the development-only integration before production.

Visual AI Bridge does not remove or relax the target site's CSP. If the policy does not allow the injected same-origin external script, automatic mode returns a clear diagnostic page with manual-mode instructions.

## Complete workflow

1. Open the source folder in desktop VS Code.
2. Start the application's development server.
3. Run **Visual AI Bridge: Start** or open the Visual AI Bridge Activity Bar view.
4. Enter the complete local page URL.
5. Select **Open in Browser**.
6. Wait for **Runtime connected**.
7. Select **Annotate**.
8. Hover over an element and click it.
9. Enter the requested change and save.
10. Repeat for other elements.
11. Select **Send to AI**.
12. In default `chat` mode, Skopeo submits the short instruction directly to the supported IDE agent; otherwise inspect `.ai-context/` or let the configured terminal/generate-only mode continue.
13. Select **Clear Session** to remove the list, pins, generated context, temporary folder, and backup.

Sending does not clear annotations. New annotations continue after the highest current number.

## Generated files

```text
.ai-context/
├── PROMPT.md
├── context.json
├── annotation-001.html
├── annotation-001.png
├── annotation-001-markup.png
├── annotation-001-reference-001.png
├── annotation-002.html
└── annotation-002.png
```

- `PROMPT.md` contains the user instructions, visual references, placed-component identities/props, and explicit placement guidance for the coding agent.
- `context.json` contains the structured annotation payload, including drawing actions and visual-spec geometry. Pasted-image base64 is replaced with workspace-relative `sourceFile` references in generated JSON.
- `annotation-NNN.html` contains sanitized element context.
- `annotation-NNN.png` is optional and contains the original browser viewport at annotation time, including the numbered pin when capture succeeds.
- `annotation-NNN-markup.png` is produced when visual markup is saved and flattens the original screenshot with pasted references, placed components, and drawing marks.
- `annotation-NNN-reference-NNN.png` preserves each pasted visual reference separately so the AI can inspect both the source reference and its intended placement.

Atomic generation may temporarily use:

```text
.ai-context.tmp/
.ai-context.backup/
```

## Configuration

| Setting                          | Default                          | Purpose                                                                       |
| -------------------------------- | -------------------------------- | ----------------------------------------------------------------------------- |
| `visualAIBridge.runtimePort`     | `43119`                          | Preferred starting port for the combined proxy, runtime, and bridge WebSocket |
| `visualAIBridge.agentMode`       | `chat`                           | `chat`, `clipboard`, `generateOnly`, or `terminal`                            |
| `visualAIBridge.chatCommand`     | _(empty)_                        | Optional IDE command ID tried before built-in agent commands                  |
| `visualAIBridge.terminalCommand` | `aider --read ${promptPath} ...` | Visible terminal command template                                             |
| `visualAIBridge.htmlLimit`       | `500`                            | Maximum sanitized HTML characters per annotation                              |

Terminal placeholders `${promptPath}` and `${promptDir}` are shell-quoted automatically for Windows and POSIX shells. If the preferred runtime port is already occupied, Skopeo currently tries subsequent loopback ports; manual-injection URLs and health checks must use the port the extension actually started.

## Install

### Current packaged VSIX

The repository's tracked `skopeo-ui-0.1.19.vsix` has been rebuilt from the current `main` branch through the canonical check → test → build → package → site-sync pipeline. The release bundle excludes local audit/log artifacts and includes the latest browser-preview workflow:

- draggable, minimizable floating **Annotate**, **Notes**, **Send**, **Copy**, and **Clear** controls inside the real browser preview; minimizing closes open Notes/drawing UI so compact mode stays icon-only
- an active **Notes** manager with screenshot thumbnails, inline note editing/saving, and shared synchronization with the IDE panel and generated `.ai-context/`
- a layered **Draw** editor with drawing tools, multiple pasted visual references, multiple placed UI components, editable transforms/props, shared z-order, and a flattened marked-up PNG while preserving the original screenshot
- pasted-reference controls for opacity, Fit, Original Size, crop, rotation, duplicate/delete, drag/resize, and forward/backward ordering
- a searchable component palette with offline **Skopeo Core** plus a Skopeo-managed **shadcn/ui** preview library; install/update/repair/remove operate only in extension global storage, while the official registry remains metadata fallback behavior rather than the normal preview path
- structured AI context records exactly which annotation each component/reference belongs to and preserves provider/component identity, props, geometry, crop/opacity/rotation, source reference, and stacking order
- wheel/trackpad scroll containment: hovered Skopeo textareas and Notes lists scroll locally without leaking scrolling into the preview page behind the floating UI
- browser-side success/error confirmations for annotation saves/updates, AI handoff, prompt copy, and session clearing
- a dedicated **Copy Prompt** action in the IDE panel
- automatic SPA route synchronization for `pushState`, `replaceState`, back/forward navigation, and hash changes
- a **Report a Bug** action in the Skopeo panel and Command Palette that opens a structured GitHub Issue Form with Skopeo/IDE/OS details prefilled and optional screenshot or recording upload
- a no-backend update checker for manual VSIX installs: Skopeo checks the GitHub Pages `latest.json` at most once per day and offers the direct latest VSIX or Marketplace when a newer version exists; **Skopeo: Check for Updates** forces an immediate check
- the packaged 0.1.19 release baseline is **158 passing tests across 33 files** and includes the managed shadcn renderer-library workflow described below

You can install Skopeo directly from the **[Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=Domincee.skopeo-ui)**!

Alternatively, if you want to install it from the `.vsix` source:

In VS Code:

1. Open **Extensions**.
2. Select the `...` menu.
3. Choose **Install from VSIX...**.
4. Select `skopeo-ui-0.1.19.vsix`.
5. Reload VS Code when prompted.

Command line:

```bash
code --install-extension skopeo-ui-0.1.19.vsix
```

## Private development and release workflow

The actual Skopeo development repository is **`DominceAseberos/Skopeo-Source`** and must remain private. The public **`DominceAseberos/Skopeo`** repository is distribution-only and must never receive `src/`, tests, package/build configuration, or other development files.

The local development checkout is configured with:

```text
origin  -> https://github.com/DominceAseberos/Skopeo-Source.git  (PRIVATE)
public  -> https://github.com/DominceAseberos/Skopeo.git         (PUBLIC DISTRIBUTION)
```

### Normal development

Commit and push ordinary feature work, bug fixes, tests, and documentation to the private source repository only:

```bash
git add .
git commit -m "feat: describe the change"
git push
```

Because `origin` points to `Skopeo-Source`, a normal `git push` keeps the development source private. Do **not** push the development branch directly to the `public` remote.

### Publishing a new package

For a new release, use this sequence from the private source repository:

```bash
# 1. Update the package version and changelog as needed.
# 2. Build, validate, test, package the VSIX, and refresh docs/downloads/latest.json.
npm run package

# 3. Commit the release/source changes to the PRIVATE repository.
git add .
git commit -m "release: Skopeo x.y.z"
git push

# 4. Publish only the safe distribution files to the PUBLIC repository.
npm run public:publish
```

`npm run public:publish` clones the public repository into a temporary directory and copies only the approved distribution material: `README.md`, `CHANGELOG.md`, `docs/`, public icons, GitHub issue templates, and the current versioned VSIX. It intentionally does **not** copy `src/`, `test/`, `scripts/`, `package.json`, TypeScript/build configuration, or other private development files.

After `public:publish` pushes the distribution commit, GitHub Pages rebuilds the public website automatically and the stable `docs/downloads/skopeo-ui-latest.vsix` URL serves the newly packaged version.

### Update responsibilities

Skopeo currently has three different update paths:

1. **Public website / direct VSIX** — `npm run public:publish` updates the public repository. GitHub Pages deployment then happens automatically.
2. **Manual VSIX installs, Kiro, Antigravity, and compatible IDEs** — Skopeo checks the public `latest.json` feed at most once per day (when `visualAIBridge.checkForUpdates` is enabled) and notifies the user when a newer VSIX exists. **Skopeo: Check for Updates** performs an immediate check. The extension does not silently install the downloaded VSIX.
3. **VS Code Marketplace installs** — VS Code handles extension auto-update after that version has been published to the Marketplace. Publishing a new Marketplace version is a separate release step and is **not currently performed by `public:publish`**.

This separation is intentional: GitHub Pages and the manual VSIX feed require no backend or credentials, while Marketplace publishing requires the Skopeo publisher credential/PAT.

## Build and validate from source

```bash
git clone https://github.com/DominceAseberos/Skopeo-Source.git
cd Skopeo-Source
npm ci
npm run package
```

The clone command requires GitHub access to the private repository.

`npm run package` performs:

1. strict TypeScript checking
2. ESLint with zero warnings
3. Prettier verification
4. all unit and network-level compatibility tests
5. extension, webview, and runtime builds
6. an esbuild metadata audit that rejects unexpected external packages
7. versioned shadcn renderer-library artifact + SHA-256 metadata generation
8. VSIX packaging with `--no-dependencies`
9. stable website VSIX synchronization

The extension bundle includes its runtime dependencies, including `http-proxy-3` and the viewport screenshot implementation based on `html2canvas`; only the host IDE/VS Code APIs, Node built-ins, and optional `ws` native accelerators may remain external.

### Real component renderer workflow

The separate local project `C:\Projects\Skopeo-Component-Renderer` is the source of truth for real shadcn previews. It installs the actual shadcn component source, generates **61 family descriptors and 365 curated presets**, validates that every generated family has a renderer, and builds a standalone gallery/frame application.

Refresh and sync it with:

```bash
# in Skopeo-Component-Renderer
npx shadcn@latest add --all -y
npm run check
npm run build

# in Skopeo-Source
npm run renderer:sync
npm run renderer:package
npm run check
npm run test:unit
npm run build
```

`renderer:sync` copies only the renderer project's compiled `dist/` snapshot into `vendor/component-renderer/`. Skopeo's own build copies that snapshot to `dist/component-renderer/`; `vendor/**` is excluded from the VSIX so the compiled renderer is packaged once without shipping the renderer project's source or `node_modules`.

Current source-tree automated suite:

```text
33 test files
158 passing tests
```

The 0.1.19 coverage includes Draw side-panel/scroll containment, renderer-manifest validation, managed-library install/update/repair/remove, staged SHA-256 verification and rollback, managed→bundled fallback, catalog/server hot-swapping, sandboxed renderer-frame URLs/capture messaging, Provider → Category → Family → Preset browsing, exact preset placement, renderer-aware protocol validation, and AI-context serialization of preset/version identity.

## Security and privacy

- The combined server binds only to `127.0.0.1`.
- Target URLs are restricted to HTTP loopback hosts.
- A trusted local file workspace is required.
- The webview uses a nonce-based CSP.
- Each configured proxy target receives a new random token.
- The reserved bridge WebSocket validates the token before it can replace the active socket.
- WebSocket `Origin` must exactly match the proxy origin; only the configured target origin is additionally accepted for manual fallback.
- Other WebSocket paths are forwarded to the development server for HMR.
- Target CSP headers are preserved; incompatible strict policies produce a visible manual-mode diagnostic.
- Runtime shutdown removes event listeners, pending animation frames, pins, overlays, the comment editor, reconnect timers, and browser sockets.
- Page HTML and screenshots are never written to extension logs.
- Form values, editable text, scripts, styles, inline event handlers, and bridge UI are removed from captured HTML.
- Screenshots are limited to 4 MB and full messages to 40 MB.
- No Visual AI Bridge cloud service receives the captured data.
- The shadcn preview library is separate from the user workspace. **Install Library**, **Update**, and **Repair** download only Skopeo's versioned prebuilt renderer package into extension global storage. The extension requires the approved Skopeo download path, checks the package byte limit and SHA-256, verifies every file hash and safe relative path, validates the renderer manifest/counts, stages the installation, and atomically activates it with rollback. **Remove** deletes only the managed extension-storage copy and falls back to the bundled renderer. Annotation text, screenshots, DOM/CSS context, and workspace source are never sent with library downloads or registry fallback requests.

## Known limitations

- Automatic mode changes the browser-visible origin and may affect origin-sensitive applications.
- A nonce-only or restrictive CSP may require manual injection.
- Only one browser runtime is active per bridge session.
- Only HTTP loopback applications are supported.
- Viewport screenshots are DOM-rendered with `html2canvas`, not operating-system screen captures. Cross-origin images, video, WebGL, canvas, iframes, advanced native controls, and some highly transformed/animated content may not render perfectly.
- Automatic source-component detection is not included.
- Remote workspaces, multi-root generation, collaboration, and production-site annotation are outside the current scope.

## Troubleshooting

### Automatic injection was blocked

The target CSP does not authorize the injected external script. Visual AI Bridge intentionally does not weaken it. Follow the diagnostic page and the manual-injection steps above, including the required script nonce and `connect-src` allowance.

### The panel stays on “Waiting for browser”

Check that:

- the target development server is running
- the URL uses HTTP and a loopback hostname
- the configured starting port is available, or Skopeo can select a later loopback port automatically
- the opened page is the Visual AI Bridge proxy URL
- manual mode's CSP permits both the runtime script and bridge WebSocket

Default health endpoint (replace `43119` if Skopeo started on a later port):

```text
http://127.0.0.1:43119/health
```

After a target is configured, the response includes its origin:

```json
{ "ok": true, "proxyTarget": "http://localhost:3000" }
```

### A previous browser tab disconnects

This is expected. The newest correctly authenticated runtime becomes active. A connection with a missing token, wrong token, or wrong Origin cannot replace it.

### Clear Session failed

The UI is re-enabled after generated, error, cleared, clear-error, agent-error, stopped, and unexpected-extension terminal results. If filesystem cleanup still fails, check file permissions or processes holding `.ai-context/`, then retry.

## License

MIT
