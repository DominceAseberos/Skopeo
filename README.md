# Skopeo

![Beta](https://img.shields.io/badge/beta-0.1.24--beta.4-f59e0b)
![Stable](https://img.shields.io/badge/stable-0.1.23-2563eb)
[![Marketplace](https://img.shields.io/badge/VS%20Code-Marketplace-007ACC?logo=visualstudiocode&logoColor=white)](https://marketplace.visualstudio.com/items?itemName=Domincee.skopeo-ui)
![Local First](https://img.shields.io/badge/local-first-16a34a)
![Agent Eyes](https://img.shields.io/badge/MCP-Agent%20Eyes-0891b2)
![License](https://img.shields.io/badge/license-MIT-64748b)

**Skopeo is a visual collaboration tool for developers and AI coding agents.**

It lets you open a running local web application, point to the exact interface you want changed, and describe the result directly on the page. Instead of trying to explain which card, button, section, spacing issue, or visual state you mean, you can select the real rendered element, annotate it, draw over it, attach references, place component ideas, and send that visual intent to your coding agent.

Skopeo reduces the communication gap between **what you see in the browser** and **what your coding agent needs to understand**.

Skopeo is not another coding agent. It gives the coding agents you already use a visual collaboration surface around the real running interface.

## What you can do

- Point to exact UI elements and describe what should change.
- Create visual annotations directly on a running local application.
- Draw over screenshots to explain layout, spacing, positioning, hierarchy, or design ideas.
- Paste visual references and place them exactly where they matter.
- Place real UI component previews to demonstrate a proposed result.
- Preview and interact with supported components before accepting them into Draw.
- Manage overlapping visual items from Layers / Added Items.
- Review and edit active annotations from the browser.
- Send structured visual context to a coding agent.
- Give MCP-compatible coding agents live browser awareness with Agent Eyes.
- Receive questions, inspection pins, component proposals, and markup proposals from an agent.
- Keep agent proposals pending until a developer explicitly reviews them.
- Capture and compare rendered interface state before and after changes.

## The idea

**See it -> Point at it -> Explain it -> Send it to your coding agent.**

Skopeo acts as the visual communication layer between the developer, the running application, and the coding agent.

## Quick start

1. Install Skopeo from the Visual Studio Code Marketplace or from a Skopeo VSIX release.
2. Open your local web project and start its development server.
3. Open the **Skopeo** Activity Bar view.
4. Enter the local app URL and open it through Skopeo.
5. Turn on **Annotate** and select the UI you want changed.
6. Add your instruction. Use **Draw** when the request is easier to explain visually.
7. Use **Send to AI** or **Copy Prompt**.
8. Review the generated `.ai-context/` when needed.

## Draw and component libraries

Draw supports freehand markup, arrows, rectangles, circles, text, pasted image references, layer ordering, and placed UI components.

The component toolbox separates **Components** from **Libraries**, so browsing UI presets stays focused while install/update/repair controls live in their own workspace. It includes managed previews for:

| Library   | Families | Presets |
| --------- | -------: | ------: |
| shadcn/ui |       61 |     365 |
| Mantine   |       20 |      61 |

Libraries are managed by Skopeo for preview use. Installing or updating a preview library does not add that library to the inspected application.

## Agent Eyes

Agent Eyes gives MCP-compatible coding agents access to Skopeo's visual collaboration surface.

Depending on the selected collaboration mode, an agent can:

- inspect a live selector;
- read accepted annotations and Draw specifications;
- query available component presets;
- place persistent inspection pins;
- ask the developer a question in the browser;
- propose a component or markup change for review;
- capture and compare rendered visual snapshots.

Agent observations and pending proposals remain separate from accepted human annotation intent until the developer explicitly accepts them. Inspect and Collaborate modes can be switched in place from Skopeo without navigating away from the inspected app, and pending visual proposals can be hidden temporarily without being accepted or rejected.

Use the Agent Eye link in the Skopeo panel to open the local endpoint status page. Use **Skopeo: Copy Agent Eyes MCP Client Config** only when configuring an MCP-compatible coding client.

## Supported environments

Skopeo's extension UI targets desktop **Visual Studio Code** and compatible VS Code-based IDEs that support the extension APIs it uses. Antigravity and Kiro are known compatible environments.

External coding agents or IDEs may use Agent Eyes when they support MCP. Skopeo does not currently ship a native JetBrains plugin and does not claim JetBrains-native extension UI support.

Local preview targets are intended for HTTP loopback development servers such as:

```text
http://localhost:3000
http://127.0.0.1:5173
```

## Release channels

Skopeo has separate release channels:

- **Stable** — proven release line. Current stable: `0.1.23`.
- **Beta** — opt-in prerelease line for newer features. Current beta source line: `0.1.24-beta.4`.

The update channel can be selected in Skopeo settings.

## Privacy

Skopeo is local-first. The normal annotation workflow does not require a Skopeo account or hosted Skopeo backend. Generated visual context is written into the current local workspace.

Component-library updates and release checks may fetch Skopeo distribution assets, but annotation text, screenshots, DOM context, and project source are not uploaded as part of those downloads.

## License

MIT
