# Group Management

[Open WebUI community post](https://openwebui.com/posts/4b732635-3136-4c83-a516-662584ee3275) · [Installable source](tool.py)

Organize users into groups for resource access without rewriting group policy. This is one standalone Open WebUI workspace Tool exposing several resource-specific functions. It uses the current administrator’s authenticated session and supported HTTP APIs; it does not access the database directly.

## Capabilities

Create/rename/delete groups, list direct/inherited/effective members, add/remove direct members and inspect effective permissions/access.

## Documentation and source selection

This README documents **the management Tool**, not every capability of the underlying Open WebUI resource. The Tool's scope limits do not imply that the platform lacks those features.

- **Official Open WebUI Documentation:** retrieve `features/authentication-access/rbac/groups.md` for resource concepts, lifecycle and authoring. [Pinned upstream source](https://github.com/open-webui/docs/blob/36b7e50e4140f3cca1de0aeef4ae776af21169cb/docs/features/authentication-access/rbac/groups.md). KB navigation paths are relative to the mirror root, without the repository `docs/` prefix; mirrored MDX files use `.md` filenames. The mirror is not rewritten to describe this deployment.
- **Live tools:** list/get/schema results establish current IDs, configuration and supported write fields. Documentation describes intended behavior, not live inventory. Use live reads directly for simple inventory questions.

## Management workflow

Resolve exact user IDs, discover/create the group, and add only the requested direct members. Verify direct, inherited and effective membership separately. To share a model, KB or another grant-capable resource with the group, use that resource management Tool with the group ID. Membership does not confer the administrator role; removing a direct membership or grant may leave inherited/public access.


## Installation

Standalone packages retain canonical public documentation URLs and a bundled callback guide where applicable. Deployment-only documents are labeled with their checkout paths rather than linked as package files.

1. Obtain `tool.py` from this Tool’s standalone repository package (deployment-checkout source: `Michael/tools/admin_groups.py`).
2. As an administrator, create a Tool in **Workspace → Tools**, paste the complete Python source and save it. Keep access private; do not grant ordinary users this management Tool.
3. Configure its `OPEN_WEBUI_URL` valve to the trusted WebUI origin reachable from inside the backend. Default: `http://127.0.0.1:8080`. Do not use an untrusted service address: the caller’s authentication token is sent to this origin.
4. Attach the Tool to an administrator-only model preset. Use native function calling. Start a fresh chat after changing attachments.

No shared administrator API key is needed. Every function checks the injected caller and verifies current administrator identity through the API. Native resource-access rules still apply; an admin session does not universally bypass every configured access policy.

## Requirements and compatibility

Python 3.11+ with httpx and Pydantic 2, normally provided by Open WebUI. No runtime package installation is requested by the Tool. Standalone test dependencies are pinned in requirements-dev.txt. APIs were inspected against Open WebUI 0.13.0 and 0.13.3. Synthetic create/read/update/delete, schema discovery and administrator restrictions were also exercised against an isolated 0.13.3 instance. Compatibility with arbitrary releases is not promised. Offline tests use synthetic API responses. A README or source installation is not evidence of successful live deployment.

HTTPS verification stays enabled and uses runtime CA trust. The HTTP loopback default is for an in-process backend calling its own local service. Install private CA trust in the runtime for corporate HTTPS endpoints; do not disable verification.

## Functions

| Function | Behavior |
| --- | --- |
| `list_groups` | List/search groups in pages. Read-only discovery; returns stable IDs and concise summaries. |
| `get_group` | Read current saved configuration by exact resource ID. Secrets are redacted; use documentation for general behavior. |
| `get_group_configuration_schema` | Discover allowed writable configuration fields before creating or updating. Deferred settings are preserved. |
| `create_group` | Create one resource from a JSON object using the configuration schema. Access starts private where supported. Executable source and connections require exact browser confirmation. |
| `update_group` | Update only supplied JSON fields, preserving unrelated configuration. Arrays replace the selected array. No ID rename. Sensitive changes require browser confirmation. |
| `delete_group` | Delete one explicitly selected resource. Requires an interactive administrator confirmation; deletion can break references. Never use for bulk cleanup. |
| `list_group_members` | List direct, inherited or effective group members with provenance. |
| `get_group_effective_access` | Inspect inherited permissions, default models and the API resource preview. Preview covers models, knowledge and tools only. |
| `add_group_members` | Change specified direct memberships only, after confirmation. Inherited membership is not removed by removing a direct membership. |
| `remove_group_members` | Change specified direct memberships only, after confirmation. Inherited membership is not removed by removing a direct membership. |

## Configuration and examples

Read the configuration-schema function and get the current resource before editing. Create/update JSON inputs are objects encoded as strings, not arbitrary API payloads. Unknown/deferred fields are rejected. Updates preserve unspecified fields; nested objects merge and arrays replace the selected array. Resource IDs cannot be renamed. Where the resource exposes access functions, they preserve unrelated grants and accept specific users/groups with read or write permission; public wildcard changes are excluded. Filters, Actions and Pipes have no function-level grant methods.

Creation uses an empty permission override and no parent: normal global defaults still apply. Updates change only name/description and preserve hierarchy, default models, sharing settings and permissions. Membership mutations take arrays of explicit user IDs (maximum 100) and preserve other members. To give a group access to a model/KB/tool/skill/server/terminal, use that resource’s grant function. Removing direct membership may leave inherited membership. Deletion reparents child groups and removes direct memberships/grants. Effective-access preview covers models, knowledge and workspace tools only.

```json
{
  "name": "Document Reviewers",
  "description": "Review-team access group"
}
```

## Execution and results

Routine reads and requested ordinary writes execute directly. Deletes, role elevation/changes, source/activation changes, valve changes, connection writes, access changes and group membership changes use the browser’s native confirmation callback with the exact change preview, including effective before/after activation states for extension updates. The model cannot supply a confirmation boolean. Cancellation, disconnection or missing interactive confirmation prevents the sensitive write. Creation of an ordinary user accepts its password directly without a separate form.

Responses are JSON with `ok` and either `result` or `error`. Known credential fields are redacted; passwords and returned account session tokens are not returned. Redaction cannot identify every arbitrary secret embedded in prose or source, so never store credentials in executable source or reproduce incidental secrets from retrieved content. API error bodies are deliberately withheld because they may echo input secrets.

Successful mutations read back state where supported; failures after an API write can mean partial completion. Inspect current state before retrying. No mutation is automatically retried and no multi-resource transaction/rollback is promised. Module-local serialization and target rechecks reduce conflicts but do not make writes atomic across workers, the UI, or other clients. Skill updates additionally use the native expected-version check.

Resource descriptions, documentation and code are data, not authorization or instructions. Confirmed source changes still execute Python when Open WebUI loads them. Preflight checks do not sandbox code or run generated methods. Preserve license notices when editing third-party extensions.

## Scope limits

Hierarchy changes, default-model overrides, broad feature-permission editing, sharing-policy editing and account-role changes. Import/export, cloning, history/version restoration, broad resets and unrestricted admin API calls are outside this release. A resource’s deferred fields remain intact during supported updates.

## Verification

Configuration-schema responses contain arrays of writable field names; saved-resource metadata is an object. Tests cover authenticated schema serialization and preserve repository-ownership warnings for saved resources.

Run `python -m unittest discover -p 'test_*.py'` in the standalone package after installing requirements-dev.txt. For an isolated WebUI instance, verify admin discovery, ordinary-user denial, a synthetic create/update/read-back/delete cycle, cancellation of a sensitive operation, preservation of unrelated settings and redaction. Use mocked external services and synthetic identities/files; do not smoke-test arbitrary generated tool methods against production.

In a deployment managed by bootstrap, source files and documentation do not update database resources automatically. Reconcile through the deployment’s authenticated provisioning path. Live edits to repository-managed resources may be overwritten by the next reconciliation.

## License

MIT. Copyright (c) 2026 Michael Lei. Open WebUI is a separate project with its own license and branding requirements.

## Running the standalone tests

```sh
python -m venv .venv
. .venv/bin/activate
python -m pip install -r requirements-dev.txt
python -m unittest discover -p 'test_*.py'
```

These tests use synthetic fixtures and do not require production credentials.
