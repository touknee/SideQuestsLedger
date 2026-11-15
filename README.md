# SideQuests Ledger

GitHub-backed React + TypeScript progressive web app for browsing SideQuests projects, tracks, milestones and Brain Vault records.

## Run

```bash
npm install
npm run dev
npm run build
```

## MVP workflow

1. Create a GitHub **fine-grained personal access token** scoped to the repositories containing your ledgers, with **Contents: Read and write** permissions. Do not paste a token into issues, commits, or chat.
2. Connect using the app. The token is held in browser sessionStorage, not the repo. Disconnect clears it. **This is temporary token authentication, not GitHub OAuth.**
3. Select the repositories holding the canonical records and Sync. The app discovers Markdown/JSON/YAML files with matching paths/IDs and loads them without generating data.
4. Open a record, edit its source, and commit the change back to its existing GitHub file. GitHub SHA checks reject stale writes. You can also create a new Markdown file.

## Scope and limitations

- **Early MVP, not yet verified in a browser or deployed.** The current UI is a source-file editor, not a structured milestone/task form. Matching is heuristic and some records may be unrecognized or duplicated. No records are fabricated.
- Authentication must be upgraded to a backend-supported GitHub OAuth flow (PKCE where applicable) before a public release. Avoid granting broad repository access. Session storage reduces persistence but does not eliminate XSS risks.
- Writes currently commit directly to the selected repository's default branch; a review/PR workflow, conflict UI, validation and Change Journal integration are planned.
- PWA shell caching is best-effort; offline editing/sync is not implemented. Do not assume all ledger data is available offline.
- No GitHub Actions build is added to avoid consuming Actions minutes; build locally or through a separate deployment provider.

## Next milestones

1. Identify canonical ledger schema and source repositories, implement parsers with fixtures and tests.
2. Structured CRUD for projects, tracks, milestones and Brain Vault with validated IDs, relationships and Change Journal updates.
3. GitHub OAuth via a secure server-side exchange, least-privilege scopes and optional PR-based changes.
4. Automated tests, PWA icons/assets, deployment, offline read cache and conflict handling.

## Structured workspace (development branch)

The new **SideQuests workspace** supports form-based creation of tracks and SideQuests (projects, experiences, experiments, and ideas), with optional track assignment and allowed type selection. Each structured track/SideQuest supports milestones, tasks, notes, completion checkboxes and status updates. Changes write JSON to `ledger/tracks/T###.json` or `ledger/sidequests/{P,E,X,I}###.json` using GitHub's Contents API and file SHA optimistic concurrency checks. IDs are computed from loaded selected-repository records, using max existing number + 1; repository sync is required before creating records. Concurrent creations may still collide, in which case refresh and retry.

**Migration limitation:** Legacy Markdown tracks and projects are displayed in the raw records browser, but are **not yet editable in structured forms**. They must be mapped to the canonical schema first; the app does not rewrite or fabricate legacy data. Track child relationships currently cover structured records only. Notes, tasks and milestones are stored inline in the parent JSON document. For now, the advanced source editor remains available for existing files. There is no automatic Change Journal entry yet.
