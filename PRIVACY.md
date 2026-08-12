# Privacy

Skopeo is designed as a local-first developer tool. This document describes the behavior of the public stable 0.1.24 extension (`Domincee.skopeo-ui`).

## Data handled locally

When you use Skopeo, it may process data from the local development application you choose to inspect, including:

- the local preview URL;
- annotation text and visual markup;
- screenshots created for visual context;
- selected DOM/HTML and computed style context;
- selectors, geometry, routes, and component-placement information; and
- generated prompt/context files.

The normal workflow stores generated context in the current workspace, including `.ai-context/`. Skopeo does not require a Skopeo account or a Skopeo-operated hosted backend for this workflow.

## External network requests

Stable 0.1.24 can make limited outbound requests for product functionality:

- `raw.githubusercontent.com/DominceAseberos/Skopeo/...` for managed component-renderer metadata or distribution assets;
- `dominceaseberos.github.io/Skopeo/downloads/...` for Skopeo release/update metadata and manual VSIX downloads;
- the Visual Studio Marketplace when you explicitly choose to open the Marketplace; and
- GitHub when you explicitly choose **Report a Bug**.

The bug-report action opens a GitHub issue URL prefilled with the Skopeo version, IDE/app name and version, operating-system platform, and CPU architecture. The report is not submitted until you choose to submit it on GitHub.

Skopeo's local browser preview is intended for HTTP loopback development servers such as `localhost` and `127.0.0.1`.

## Telemetry

The stable 0.1.24 package does not use a Skopeo-operated analytics or telemetry backend. Annotation text, screenshots, DOM context, and project source are not uploaded to a Skopeo service as part of the normal annotation workflow.

Third-party services that receive an outbound request may process ordinary request metadata such as IP address, request headers, and server logs under their own privacy policies.

## Retention and deletion

Skopeo does not operate a server-side account or data store for normal annotation data. Workspace context remains on your machine until you remove it. Use Skopeo's clear-session workflow or delete `.ai-context/` to remove generated local context.

External services may retain their own access logs according to their policies.

## Changes

If Skopeo later introduces hosted services, analytics, accounts, or other data collection, this notice should be updated before those features are published.
