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

## Idea inbox and editable records (in progress)

- Capture an informal idea from the workspace without reserving a formal SideQuest ID. Drafts live under `ledger/drafts/DRAFT-<uuid>.json`.
- Promote an idea into a SideQuest and assign a P/E/X/I-series ID at promotion. The original draft is marked Promoted with a link to the new ID; if the second commit fails, reconcile manually before retrying.
- Create SideQuests without a track, then assign or reassign them later through the details form. Track accepted-kind rules constrain assignment.
- Edit names, descriptions, status, track assignment and track allowed kinds via forms. Remove individual tasks, milestones or notes; Git history retains old versions.
- Discarded ideas remain stored for audit rather than being permanently deleted.

**Known limitations:** Existing Markdown records are not yet migrated to the structured editor; the advanced raw-file editor remains accessible. Creating and promoting use multiple independent GitHub writes, not atomic transactions. This branch has not yet passed an automated build or browser test. Structured records need validation, conflict/retry improvements and a proper GitHub OAuth flow before production deployment.

## Repository bindings and progress ownership (initial implementation)

Each structured track or SideQuest can link an accessible GitHub repository and branch through its Edit details form. The binding stores the repository full name, branch, a progress ownership choice (`ledger` or `linked`), and an optional progress document path. The detail screen fetches the latest commit from the linked repository and links to the repository. This is **activity evidence**, not verified milestone completion.

**Important:** The ownership choice is metadata only in this version. It does not yet route milestone/task writes into the linked repository, parse its progress document, or reconcile duplicate documents. Structured milestones still write to the SideQuests Ledger record. Implement explicit progress adapters and source-aware write routing before describing linked repositories as authoritative in practice. Existing Markdown projects are not yet covered by structured repository bindings.
