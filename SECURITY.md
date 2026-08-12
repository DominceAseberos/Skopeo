# Security

## Supported releases

Security fixes are prioritized for the latest stable Skopeo release. Beta/prerelease builds are experimental and may change more frequently.

## Reporting a vulnerability

Please do not publish credentials, exploit details, private project data, or other sensitive information in a public issue.

Prefer GitHub's private vulnerability reporting flow from the repository **Security** tab when it is available. If private vulnerability reporting is unavailable, contact the publisher through the `DominceAseberos` GitHub profile and request a private reporting channel before sharing sensitive details.

Include, when relevant:

- the Skopeo version;
- VS Code or compatible IDE version;
- operating system;
- a minimal reproduction;
- affected local URL/route without private credentials;
- expected versus observed behavior; and
- the security impact.

## Security-sensitive areas

Reports are especially useful for issues involving the local preview proxy, browser-runtime authentication, WebSocket communication, workspace file writes, command/agent handoff, component-library downloads, update handling, or unexpected outbound network access.

## Secrets

Never include access tokens, API keys, passwords, private keys, or production `.env` contents in bug reports or example projects.
