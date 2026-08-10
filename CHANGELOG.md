# Changelog

Public release notes for Skopeo. Detailed engineering history is maintained in the private source repository.

## Unreleased

## 0.1.24-beta.4 - 2026-08-11

- Agent Eye now opens a local status page instead of copying an endpoint URL from the inline action. Full MCP client configuration remains a separate explicit command.
- Inspect and Collaborate modes can be changed in place from both the Skopeo sidebar and live browser Agent panel without leaving the inspected application.
- Agent questions and visual proposals remain non-intrusive until the developer opens them for review.
- Pending component and markup proposals can be hidden and shown again without accepting or rejecting them.
- The component toolbox now separates **Components** and **Libraries** into focused workspaces, with library update/error attention states.
- Clarified compatibility: the Skopeo extension UI targets VS Code and compatible VS Code-based IDEs; external MCP-compatible agents can connect through Agent Eyes.
- Refined public documentation to focus on product capabilities while keeping implementation and architecture documentation private.

## 0.1.24-beta.3 - 2026-08-10

- Fixed annotation-note focus and touch interactions that could interrupt typing or prevent Draw from opening on touch devices.
- Improved annotation synchronization so active note editing remains stable during browser/session updates.
- Reworked the Skopeo sidebar into a compact, scrollable Preview / Agent Eye workspace.
- Improved responsive behavior for short and narrow IDE sidebars.
- Updated the Skopeo panel branding and status presentation.

## 0.1.24-beta.2 - 2026-08-10

- Fixed the Annotate toggle being visually or behaviorally forced back on by stale state updates.
- Fixed the preview URL field reverting to a previous localhost URL while editing or reconnecting.
- Stabilized the empty annotation state to prevent flicker and layout jumping.

## 0.1.24-beta.1 - 2026-08-10

- Introduced **Agent Eyes** for bidirectional browser and coding-agent collaboration.
- Added live selector inspection, persistent Agent inspection pins, human questions, visual proposals, and before/after verification.
- Added explicit Off / Inspect / Collaborate modes.
- Modernized the Skopeo sidebar and responsive Draw workspace.
- Added Layers / Added Items for managing placed components and pasted references.

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
