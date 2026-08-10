# Skopeo Agent Eyes / Bidirectional MCP Implementation Plan

> Status: Implemented and closure-validated in Skopeo 0.1.24-beta.1
> Baseline: Skopeo 0.1.24-beta.1
> Last updated: 2026-08-10
> Scope: Extension-host control plane, agent-driven browser inspection, MCP exposure, human review, and agent visual proposals.

## 1. Product goal

Skopeo should not ship an MCP server merely to expose the existing annotation list. The differentiating goal is to turn the browser into a shared visual workspace between the developer and coding agent.

Current flow:

```text
Human -> Browser -> Skopeo -> Agent
```

Target flow:

```text
                     +---------------- Human ----------------+
                     |                                       |
                     v                                       |
Browser <------ Skopeo Control Plane ------> Coding Agent    |
  ^                  |      ^                                |
  |                  |      |                                |
  +---- inspect -----+      +---- review / answer -----------+
```

The agent should be able to inspect the rendered browser itself, verify its changes, query Skopeo's component libraries, and propose visual changes back into Skopeo for human review. Human intent must remain the approval boundary.

## 2. Core product principles

- **MCP is a transport, not the feature.** Browser inspection and collaboration must work through Skopeo's internal control plane before they are exposed through MCP.
- **One inspection pipeline.** Human clicks and agent selector inspections must reuse the same DOM/CSS/viewport/screenshot extraction code.
- **Agent observation is not human intent.** Temporary agent inspections must not silently enter `.ai-context/`.
- **Agent proposals require review.** Suggested components or markup remain pending until the human explicitly accepts them.
- **No arbitrary browser automation.** Skopeo focuses on visual inspection, visual intent, review, and verification rather than becoming a generic Playwright replacement.
- **The webview is not the source of truth.** Session state must live in the extension host so MCP still works when the Skopeo panel is closed.
- **Local-first security remains mandatory.** Browser control stays loopback-only, authenticated, bounded, validated, and workspace-aware.

## 3. Existing prerequisites already available

These capabilities already exist in Skopeo 0.1.23 and should be reused rather than rebuilt.

- [x] Authenticated browser runtime WebSocket through `LocalBridgeServer`.
- [x] Extension -> browser control through `LocalBridgeServer.sendToRuntime(...)`.
- [x] Browser -> extension runtime event path.
- [x] Human annotation extraction for selector, opening tag, sanitized HTML, computed CSS, text, rect, viewport, and screenshot.
- [x] Annotation pin rendering and restoration.
- [x] Draw actions and `AnnotationDrawing`.
- [x] Pasted visual references and `AnnotationVisualSpec`.
- [x] Flattened marked-up screenshot generation.
- [x] Real renderer-backed component placement.
- [x] shadcn/ui managed catalog: 61 families / 365 presets.
- [x] Mantine managed catalog: 20 families / 61 presets.
- [x] Provider-scoped component renderer roots.
- [x] Managed-library install/update/repair/remove infrastructure.
- [x] `.ai-context/` generation with rollback-safe writes.
- [x] Preview-only interaction state is excluded from saved AI intent.

## 4. Required architecture

### 4.1 New extension-host control layer

Create:

```text
src/extension/control/
├── skopeoControlPlane.ts
├── sessionStore.ts
├── browserCommandBroker.ts
├── reviewStore.ts
└── artifactService.ts
```

Responsibilities:

```text
Browser Runtime
      |
      v
LocalBridgeServer
      |
      v
SkopeoControlPlane
 ├── SessionStore
 ├── BrowserCommandBroker
 ├── ReviewStore
 ├── ArtifactService
 └── ComponentRegistryService
      |
      +---- Webview UI
      |
      +---- MCP Server
```

The webview becomes a subscriber/client of the canonical extension-host state.

### 4.2 State categories

#### Human annotation

Persistent implementation intent created or accepted by the human.

- Included in normal Skopeo session state.
- Eligible for `.ai-context/`.
- May contain Draw actions, references, and placed components.

#### Agent inspection

Temporary agent observation of a rendered element.

- Selector/DOM/CSS/rect/viewport/screenshot.
- Ephemeral by default.
- Does not automatically become an annotation.
- Does not automatically enter `.ai-context/`.
- May optionally be shown as a temporary/persistent agent inspection pin.

#### Agent review item / proposal

A question, warning, component suggestion, or future markup suggestion requiring human review.

- `pending` by default.
- Never becomes implementation intent automatically.
- Accepted component proposals become normal `PlacedComponent` state.
- Rejected/withdrawn proposals are excluded from generated context.

## 5. Phase 1 - Canonical SessionStore and SkopeoControlPlane

**Priority: IMPLEMENT FIRST**

Goal: Move canonical session ownership out of the webview without changing visible behavior.

### Tasks

- [x] Create `src/extension/control/sessionStore.ts`.
- [x] Define extension-host `SkopeoSessionState`.
- [x] Store `sessionId`, `previewUrl`, `browserConnected`, canonical `annotations`, and `staleAnnotationIds`.
- [x] Add `getSnapshot()`.
- [x] Add `subscribe(listener)` and unsubscribe support.
- [x] Add `setBrowserConnected(...)` and `setPreviewUrl(...)`.
- [x] Add `addAnnotation(...)`, `updateAnnotation(...)`, `deleteAnnotation(...)`, and `replaceAnnotations(...)`.
- [x] Add `updateInstruction(...)` and `updateComposition(...)`.
- [x] Add stale/restored annotation helpers.
- [x] Add `clear()` / session reset behavior.
- [x] Create `src/extension/control/skopeoControlPlane.ts`.
- [x] Route validated runtime events through the control plane before webview notification.
- [x] Move browser connection/disconnection state into `SessionStore`.
- [x] Make the webview synchronize from extension-host snapshots/events.
- [x] Remove webview ownership as the canonical session source.
- [x] Keep webview persistence only as a UI/cache compatibility layer if still needed.
- [x] Ensure reopening the Skopeo view reconstructs UI from `SessionStore`.
- [x] Keep annotation numbering/restoration behavior unchanged.
- [x] Keep Notes, Draw, Delete, Restore, Send, Copy Prompt, and context generation behavior unchanged.

### Phase 1 acceptance criteria

- [x] Closing the Skopeo panel does not destroy the active extension-host session.
- [x] Reopening the panel restores current annotations from extension state.
- [x] Human annotation creation works exactly as before.
- [x] Instruction edits synchronize to browser and extension state.
- [x] Draw composition changes persist correctly.
- [x] Delete and stale-selector restoration still work.
- [x] `.ai-context/` output remains equivalent to pre-refactor behavior.
- [x] Existing tests remain green.
- [x] Add focused unit tests for `SessionStore` and control-plane event routing.

## 6. Phase 2 - BrowserCommandBroker request/response protocol

Goal: Convert fire-and-forget extension -> browser messaging into a bounded request/response channel usable by agent features.

### Tasks

- [x] Create `src/extension/control/browserCommandBroker.ts`.
- [x] Generate a unique `requestId` for each command.
- [x] Track pending requests in a bounded map.
- [x] Resolve responses by matching `requestId`.
- [x] Reject pending requests when the browser disconnects.
- [x] Add request timeouts.
- [x] Add structured `BROWSER_NOT_CONNECTED`, `REQUEST_TIMEOUT`, and `INVALID_RESPONSE` errors.
- [x] Limit maximum pending requests.
- [x] Validate request/response message sizes.
- [x] Add a lightweight ping/health request.
- [x] Add shared request/response types in `src/shared/messages.ts`.
- [x] Add validator coverage for all new message variants.

### Phase 2 acceptance criteria

- [x] Extension sends a browser command and receives the correlated response.
- [x] Out-of-order responses resolve the correct promises.
- [x] Disconnect rejects pending commands cleanly.
- [x] Timeout leaves no stale pending request.
- [x] Invalid or unknown response IDs are ignored safely.

## 7. Phase 3 - Reusable browser element inspection primitive

Goal: Split human annotation creation from element inspection/capture work.

Current `AnnotationEngine.saveAnnotation(...)` mixes both responsibilities. Refactor toward:

```ts
inspectElement(element, options): Promise<ElementInspection>
createHumanAnnotation(inspection, instruction): Annotation
```

Proposed inspection shape:

```ts
interface ElementInspection {
  pageUrl: string;
  pageTitle: string;
  targetTag: string;
  openingTag: string;
  cssPath: string;
  textExcerpt: string;
  truncatedHTML: string;
  computedCSS: string;
  elementRect: ElementRect;
  viewport: ViewportData;
  screenshotBase64: string;
  capturedAt: string;
}
```

### Tasks

- [x] Extract reusable DOM/CSS/text/rect/viewport inspection logic from `saveAnnotation()`.
- [x] Extract reusable screenshot capture logic.
- [x] Keep pin inclusion explicit for human annotations.
- [x] Support inspection without creating a human pin.
- [x] Preserve HTML and screenshot payload limits.
- [x] Preserve Skopeo UI-element exclusion.
- [x] Add `runtime.inspectSelector` browser command.
- [x] Query selectors safely with CSS syntax error handling.
- [x] Require exactly one match by default.
- [x] Return `SELECTOR_NOT_FOUND` for zero matches.
- [x] Return `SELECTOR_AMBIGUOUS` with `matchCount` for multiple matches.
- [x] Add optional `matchIndex` only after the single-match path is stable.
- [x] Support optional `scroll: "if-needed"`.
- [x] Preserve/restore the user's original scroll position after off-screen inspection.
- [x] Wait for a render frame after programmatic scrolling before capture.
- [x] Return inspection results through `BrowserCommandBroker`.

### Phase 3 acceptance criteria

- [x] Human annotations still use the same visual extraction results.
- [x] Selector inspection returns full element context without creating an annotation.
- [x] Off-screen elements can be inspected without permanently moving the user's viewport.
- [x] Ambiguous selectors never silently choose an arbitrary element.

## 8. Phase 4 - Internal Agent Eyes API before MCP

Goal: Prove the feature inside Skopeo before binding it to MCP.

```ts
controlPlane.getStatus();
controlPlane.listAnnotations();
controlPlane.getAnnotation(id);
controlPlane.getMarkupSpec(id);
controlPlane.inspectSelector(input);
controlPlane.listComponents(input);
controlPlane.getComponentSpec(input);
```

### Tasks

- [x] Implement `getStatus()` from canonical extension state.
- [x] Implement lightweight `listAnnotations()`.
- [x] Implement `getAnnotation(id)`.
- [x] Implement `getMarkupSpec(annotationId)` without pasted-image base64 payloads.
- [x] Implement artifact lookup for marked-up screenshots.
- [x] Implement provider-aware `listComponents(...)` using `ComponentRegistryService`.
- [x] Implement `getComponentSpec(...)`.
- [x] Implement `inspectSelector(...)` using `BrowserCommandBroker`.
- [x] Add internal integration tests without MCP transport.

## 9. Phase 5 - Minimal MCP server

Goal: Expose the proven control-plane API to coding agents.

Preferred architecture:

```text
MCP transport
    |
    v
Skopeo MCP adapter
    |
    v
SkopeoControlPlane
```

The MCP adapter must contain almost no browser/business logic.

### Initial MCP tools

- [x] `skopeo_status`
- [x] `list_annotations`
- [x] `get_annotation`
- [x] `get_markup_spec`
- [x] `get_markup_png`
- [x] `list_components`
- [x] `get_component_spec`
- [x] `inspect_selector`

### Transport tasks

- [x] Decide first supported transport: loopback HTTP MCP or stdio.
- [x] Keep protocol transport separate from Skopeo domain logic.
- [x] Authenticate loopback MCP if HTTP transport is used.
- [x] Bind only to loopback by default.
- [x] Surface MCP connection/status in Skopeo UI/settings.
- [x] Add tool-level input schemas and bounds.
- [x] Return structured errors rather than raw exceptions.
- [x] Return images through MCP image/resource semantics rather than giant JSON base64 fields where possible.
- [x] Add MCP server lifecycle to extension activation/deactivation.

## 10. Proposed MCP V1 tool behavior

### `skopeo_status`

Returns browser connection state, current preview URL, session ID, human annotation count, pending review count, and installed component-provider counts.

### `list_annotations`

Return lightweight metadata only: ID/number, instruction, selector, Draw/markup presence, reference count, and component count.

### `get_annotation`

Return detailed human annotation context without unnecessarily embedding every large image in JSON.

### `get_markup_spec`

Return drawing actions, pasted-reference geometry/crop/opacity/rotation/z-index, placed component provider/component/preset/props/geometry/appearance metadata, and artifact references instead of inline pasted-image base64.

### `get_markup_png`

Return the flattened `annotation-NNN-markup.png` visual artifact.

### `list_components`

Inputs should support provider ID or `all`, search query, and optional category filter.

### `get_component_spec`

Return provider, component/family ID, preset ID, default size, default props, editable prop definitions/options, and useful renderer/source metadata.

### `inspect_selector`

Suggested input:

```json
{
  "selector": ".sidebar > button.primary",
  "note": "Verify the rendered button after implementation",
  "scroll": "if-needed",
  "persistPin": false
}
```

Default `inspect_selector` behavior remains ephemeral. In Collaborate mode, agents can use `pin_selector`, `list_inspection_pins`, and `remove_inspection_pin` to maintain bounded session-scoped `?` inspection pins without converting those observations into annotations or `.ai-context/` intent.

## 11. Phase 6 - Agent review and human questions

Goal: Make the browser a bidirectional human-agent collaboration surface.

Proposed review kinds:

```text
inspection
question
component_proposal
markup_proposal
warning
```

Proposed statuses:

```text
pending
answered
accepted
rejected
resolved
withdrawn
```

### Tasks

- [x] Create `src/extension/control/reviewStore.ts`.
- [x] Define validated review-item interfaces.
- [x] Add bounded review history.
- [x] Add `ask_human` control-plane capability.
- [x] Add `list_review_items` control-plane capability.
- [x] Add MCP `ask_human`.
- [x] Add MCP `list_review_items`.
- [x] Add browser-visible agent question pins.
- [x] Visually distinguish agent pins from numbered human annotation pins.
- [x] Add compact floating-toolbar Agent/Review indicator with pending count.
- [x] Add review panel/list inside the floating toolbar.
- [x] Allow human text answers.
- [x] Optionally allow bounded predefined choices.
- [x] Synchronize answers back into `ReviewStore`.
- [x] Ensure review items are not written to `.ai-context/` unless explicitly converted/accepted.

## 12. Phase 7 - Agent component proposals in Draw

Goal: Let an agent visually propose a real component without silently changing human intent.

Proposed state:

```ts
interface AgentComponentProposal {
  id: string;
  annotationId: string;
  providerId: string;
  componentId: string;
  presetId?: string;
  props: Record<string, ComponentPropValue>;
  transform: VisualObjectTransform;
  reason?: string;
  status: "pending" | "accepted" | "rejected" | "withdrawn";
  createdAt: string;
}
```

### Tasks

- [x] Add `suggest_component` control-plane capability.
- [x] Validate provider/component/preset against installed catalog.
- [x] Validate props against Skopeo's allowed component prop definitions.
- [x] Validate normalized geometry and z-index limits.
- [x] Render proposals using the real shadcn/Mantine renderer.
- [x] Give proposals a clear agent-suggestion visual treatment.
- [x] Add Accept action.
- [x] Add Reject action.
- [x] Convert accepted proposal into a normal `PlacedComponent`.
- [x] Remove rejected proposal from active review state.
- [x] Ensure pending/rejected proposals never enter `AnnotationVisualSpec` used for AI intent.
- [x] Add MCP `suggest_component`.

## 13. Phase 8 - Agent markup proposals

Goal: Later allow an agent to suggest arrows, rectangles, and text without directly modifying human Draw intent.

- [x] Define proposal-safe drawing action schema.
- [x] Add `suggest_markup` control-plane capability.
- [x] Render proposed arrows/rectangles/text separately from accepted drawing actions.
- [x] Add per-proposal or grouped Accept/Reject controls.
- [x] Merge accepted actions into `AnnotationDrawing` only after human approval.
- [x] Add MCP `suggest_markup` only after component proposals are stable.

## 14. Phase 9 - Visual verification loop

Goal: Support autonomous `implement -> inspect -> verify -> correct` workflows.

### Tasks

- [x] Add `capture_visual_snapshot(selector, label?)`.
- [x] Persist bounded snapshot metadata/artifact references in the session.
- [x] Add `compare_visual_snapshot(...)`.
- [x] Compare element rect/dimensions.
- [x] Compare selected computed-style properties.
- [x] Compare visible text where appropriate.
- [x] Provide before/after screenshot resources.
- [x] Add high-level `verify_change(selector, expectation)` only after low-level snapshot APIs are stable.
- [x] Keep final semantic judgment with the agent rather than inventing unreliable pixel-only pass/fail logic.

## 15. Agent collaboration permissions

Add a Skopeo setting such as **Agent Collaboration**.

### Off

- MCP may read existing Skopeo context if desired.
- Agent cannot issue live browser inspection commands.
- Agent cannot create review items or proposals.

### Inspect

Allowed:

- Live selector inspection.
- Browser screenshots through bounded inspection APIs.
- Component catalog queries.
- Reading existing human annotations/Draw artifacts.

Not allowed:

- Persistent agent pins.
- Human questions.
- Component/markup proposals.

### Collaborate

Adds persistent agent inspection pins, human questions, component proposals, and future markup proposals.

Still not allowed:

- Agent automatically accepting its own proposal.
- Agent silently converting observations into human annotation intent.

## 16. Explicit non-goals

Do **not** expose generic browser-execution tools such as:

- `execute_javascript`
- arbitrary `run_script`
- unrestricted `click_element`
- unrestricted `type_text`
- password/credential entry
- arbitrary form submission
- arbitrary external navigation

These are intentionally not roadmap tasks. Full browser automation belongs to dedicated automation tools. Skopeo's role is visual inspection, visual intent, collaboration, and verification.

## 17. Security and reliability requirements

- [x] All browser/MCP control paths remain local-first.
- [x] HTTP MCP, if used, binds to loopback by default.
- [x] Authenticate MCP clients where transport permits.
- [x] Continue authenticating the injected browser runtime.
- [x] Validate every MCP input before it reaches the browser.
- [x] Validate every browser command response before resolving a request.
- [x] Maintain strict message/payload byte limits.
- [x] Bound pending command count.
- [x] Bound review-item count/history.
- [x] Bound snapshot/artifact history.
- [x] Reject Skopeo's injected UI as inspection targets.
- [x] Never return browser storage/cookies/localStorage secrets through inspection tools.
- [x] Never broaden from local preview origins to arbitrary remote browsing implicitly.
- [x] Preserve trusted-workspace requirements for filesystem/context operations.
- [x] Do not write agent proposals into `.ai-context/` until human acceptance.

## 18. Test strategy

### Session/control-plane tests

- [x] SessionStore mutation coverage.
- [x] Subscription/unsubscription coverage.
- [x] Browser connect/disconnect coverage.
- [x] Annotation create/update/delete/restore coverage.
- [x] Draw composition update coverage.
- [x] Webview reconstruction from extension snapshot.

### BrowserCommandBroker tests

- [x] Request ID generation/correlation.
- [x] Concurrent requests.
- [x] Out-of-order responses.
- [x] Browser-disconnect rejection.
- [x] Timeout cleanup.
- [x] Invalid response rejection/ignore behavior.

### Selector inspection tests

- [x] Unique selector success.
- [x] Missing selector.
- [x] Ambiguous selector.
- [x] Invalid CSS selector syntax.
- [x] Skopeo UI target rejection.
- [x] Screenshot payload bounds.
- [x] HTML truncation bounds.
- [x] Scroll-into-view and viewport restoration.

### MCP tests

- [x] Tool schemas reject invalid input.
- [x] Disconnected browser returns structured errors.
- [x] Markup tools omit raw pasted-image base64 from JSON specs.
- [x] Image artifacts return through the correct MCP content/resource path.
- [x] Component catalog tools respect provider availability.
- [x] Permission modes gate live/collaboration tools.

### Collaboration tests

- [x] Pending proposals are not implementation intent.
- [x] Accept converts a proposal exactly once.
- [x] Reject never mutates `AnnotationVisualSpec`.
- [x] Human answers are visible to agent queries.
- [x] Review badge/count remains synchronized.

## 19. File impact map

Expected areas of change as implementation progresses:

```text
src/extension/control/                    NEW
src/extension/webviewProvider.ts          session/control-plane wiring
src/extension/localBridgeServer.ts        broker/lifecycle support
src/extension/extension.ts                control-plane + MCP lifecycle
src/extension/componentRegistryService.ts component MCP queries
src/extension/contextGenerator.ts         artifact access; preserve intent boundary

src/runtime/annotationEngine.ts           reusable inspectElement primitive
src/runtime/bridgeClient.ts               command responses
src/runtime/floatingToolbar.ts            agent/review UX
src/runtime/drawingEditor.ts              proposal rendering/review
src/runtime/componentLayer.ts             proposal component preview support
src/runtime/pinManager.ts                 agent inspection/question pins

src/shared/interfaces.ts                  inspection/review/proposal types
src/shared/messages.ts                    browser request/response protocol

src/webview/main.ts                       subscribe/render canonical session
src/webview/stateManager.ts               reduce canonical ownership

test/unit/                                new control-plane/broker/MCP/review tests
```

## 19.1 Implemented 0.1.24-beta.1 status

Agent Eyes / Bidirectional MCP is implemented in the 0.1.24-beta.1 source tree. The interoperable MCP surface now contains **19 tools**:

- `skopeo_status`, `list_annotations`, `get_annotation`
- `get_markup_spec`, `get_markup_png`
- `list_components`, `get_component_spec`
- `inspect_selector` with optional zero-based `matchIndex`
- `pin_selector`, `list_inspection_pins`, `remove_inspection_pin`
- `ask_human`, `list_review_items`
- `suggest_component`, `suggest_markup`
- `capture_visual_snapshot`, `list_visual_snapshots`, `compare_visual_snapshot`, `verify_change`

The browser collaboration surface now includes session-scoped persistent Agent inspection pins, exact pending-review opening, and a bounded full Agent review-history panel. Agent observations and pending proposals remain separate from human annotation / Draw implementation intent.

## 20. Recommended release sequence

Do not ship everything in one release.

### Release A - Control-plane foundation

- [x] Phase 1 SessionStore/SkopeoControlPlane.
- [x] Phase 2 BrowserCommandBroker.
- [x] No user-facing MCP claim yet.

### Release B - Agent Eyes preview

- [x] Phase 3 reusable selector inspection.
- [x] Phase 4 internal control-plane API.
- [x] Expose/debug live selector inspection internally.

### Release C - Differentiated MCP V1

- [x] Phase 5 MCP server.
- [x] Ship read/Draw/component tools plus `inspect_selector`.
- [x] Document setup for supported coding agents.

### Release D - Collaboration

- [x] Phase 6 human questions/review items.
- [x] Phase 7 component proposals.

### Release E - Verification

- [x] Phase 9 before/after visual snapshots and verification loop.

## 21. First implementation milestone - start here

The next coding task should be **only the control-plane/session refactor**.

### Sprint checklist

- [x] Add `src/extension/control/sessionStore.ts`.
- [x] Add `src/extension/control/skopeoControlPlane.ts`.
- [x] Define the canonical extension-host session snapshot.
- [x] Route browser annotation events into `SessionStore`.
- [x] Route Draw/instruction changes into `SessionStore`.
- [x] Make the webview consume control-plane snapshots/events.
- [x] Verify the Skopeo panel can be closed/reopened without losing the active session.
- [x] Verify `.ai-context/` generation remains unchanged.
- [x] Add unit tests for all SessionStore mutations.
- [x] Add control-plane routing tests.
- [x] Run TypeScript, ESLint, Prettier, complete test suite, and production build.

**Do not implement MCP transport in this first milestone.** The foundation is complete only when existing human workflows behave identically while the extension host owns canonical state.

## 22. Definition of done for Agent Eyes / Bidirectional MCP

- [x] Skopeo session state survives independently of the webview lifecycle.
- [x] Agents can determine whether a live Skopeo browser is connected.
- [x] Agents can inspect a unique CSS selector and receive rendered DOM/CSS/viewport/screenshot context.
- [x] Agent inspections are ephemeral by default and do not pollute human annotations.
- [x] Agents can read human Draw specifications and flattened markup images.
- [x] Agents can query installed shadcn/Mantine components and preset specs.
- [x] Agents can ask the human questions directly inside the Skopeo browser UX.
- [x] Agents can propose real renderer-backed components for human review.
- [x] Pending agent proposals never silently become AI implementation intent.
- [x] Human acceptance is required before a proposal enters `AnnotationVisualSpec`.
- [x] Agents can re-inspect rendered output after code changes for verification.
- [x] Permission modes clearly control read/inspect/collaboration capabilities.
- [x] No generic arbitrary-browser-execution API is introduced.
- [x] Existing annotation, Draw, library, context-generation, and release workflows remain stable.
- [x] Full validation and security tests pass before release.

## 23. Product positioning

Avoid positioning this merely as **"Skopeo MCP Server"**.

The feature concept is **Agent Eyes / Live Agent Inspection**:

> Skopeo lets coding agents inspect the real browser themselves, understand the developer's visual Draw intent, propose visual changes back into the browser, ask the human questions in context, and verify the rendered result after implementation.

MCP is the interoperable transport that makes this available to compatible coding agents; the differentiator is the bidirectional visual workflow.

## 24. 0.1.24-beta.1 closure audit

The original phase checklists are now fully closed against the implemented source tree and automated coverage. The final closure pass added the Skopeo-panel Agent Eyes status/control card, explicit concurrent/out-of-order/unknown-response BrowserCommandBroker tests, full SessionStore composition/replacement/clear mutation coverage, Agent Eyes status-message validation, and MCP schema-boundary rejection tests.

Remaining work outside this Agent Eyes implementation plan is compatibility validation for unusual origin-sensitive applications and broader product roadmap work documented in the root `planning.md`; those are not missing bidirectional MCP features.
