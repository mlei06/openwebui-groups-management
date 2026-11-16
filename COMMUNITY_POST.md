# Group Management

Create groups, organize memberships and inspect effective access from an Open WebUI administrator chat.

## What you can do

Create/rename/delete groups, list direct/inherited/effective members, add/remove direct members and inspect effective permissions/access.

## Try asking

- “Create a group named Document Reviewers.”
- “Add the two selected user accounts to Document Reviewers.”
- “Show direct and inherited members of Document Reviewers.”

Examples describe requests to your model, not guaranteed outcomes from every model. It discovers actual resource IDs and supported fields before writing. Cross-resource requests require the corresponding individual Tools or the combined Admin Toolkit.

## Install

1. Import this listing, or paste the complete `tool.py` into **Workspace → Tools → Create Tool**.
2. Keep the Tool private and attach it to an administrator-only model preset. Enable **Native** function calling.
3. Set the `OPEN_WEBUI_URL` valve to the trusted WebUI origin reachable from its backend. The default is `http://127.0.0.1:8080` for a backend calling itself. The caller’s token is sent to this origin; HTTPS uses the runtime trust store.
4. Start a fresh chat and begin with a list or get request.

No shared admin API key, custom harness or documentation knowledge base is required. Every operation verifies the caller’s current administrator identity through the Open WebUI API. Existing resource-access checks still apply.

## Changes and confirmations

Reads and ordinary requested writes run directly. Deletions, access and membership changes, role changes, executable source/activation changes, valve updates and connection writes require an interactive browser confirmation showing the proposed change. If confirmation is unavailable or cancelled, that write does not proceed.

Updates preserve unspecified settings; attachment arrays replace the selected array. Known credential fields are redacted from results. Inspect current state after a failed write before retrying. Saving executable extensions runs server-side Python through Open WebUI; validation does not sandbox that code.

## Scope and compatibility

Membership changes preserve other members and require confirmation. Use the owning resource’s management tool to grant a group access. Group hierarchy and broad feature-permission editing are outside scope.

The individual management operations were exercised against Open WebUI 0.13.3; API contracts were also inspected on 0.13.0. Other releases may need compatibility changes. Python 3.11+, httpx and Pydantic 2 are required. No runtime dependency installation is requested.

The standalone repository package includes the full operation reference, synthetic tests and MIT license. Open WebUI is a separate project with its own license and branding requirements.
