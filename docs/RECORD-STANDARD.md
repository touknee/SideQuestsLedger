# SideQuests canonical record standard — schema v1

## Authority and compatibility

- The original SideQuests repository (`touknee/SideQuests`) remains the authoritative historical source until a record is explicitly migrated and verified. Never silently replace its registry or field guides.
- Preserve permanent P/T/E, BV, IH, BB, C, and SQ-CHG namespaces and IDs. Do not allocate new IDs from assumptions.
- Store structured Ledger records as UTF-8 JSON with `schemaVersion: 1`; existing field guides, research, prose and Change Journal remain Markdown with stable source links.
- Project and track records use `ledger/sidequests/P###.json`, `ledger/tracks/T###.json`; experience records use `ledger/sidequests/E###.json`. The authoritative repository may differ after verified migration.
- An ID is immutable. A SideQuest belongs to zero or one track via `trackId`; related records use explicit `associations` and source links.
- Tasks and milestones must have stable UUIDs, title, `done` and `createdAt`. Unverified progress must not be marked done.
- `status` (work progress) and `lifecycle` (active/archived/trashed) are independent.
- `repositoryBinding` and `migration` must not claim linked authority until the destination file is copied, read back and verified.
- Keep evidence/source links and retain original Markdown as references; do not overwrite or delete it as a side effect of conversion.
- Attachments belong to the authoritative record's repository under `sidequests-assets/<ID>/`; `manifest.json` indexes binary files by ID, original filename, MIME, size, description, date and Git blob SHA. ZIPs remain downloads; images/PDFs may preview. No attachment is uploaded to a third-party host.
- GitHub SHA checks protect writes; partial cross-repository migrations require reconciliation rather than automatic deletion.

## JSON example (illustrative, not an actual existing project)

```json
{
  "schemaVersion": 1,
  "id": "P999",
  "recordType": "sidequest",
  "kind": "project",
  "name": "Example only",
  "description": "",
  "status": "Planned",
  "lifecycle": "active",
  "trackId": null,
  "associations": [],
  "milestones": [],
  "tasks": [],
  "notes": [],
  "createdAt": "2026-11-15T00:00:00.000Z",
  "updatedAt": "2026-11-15T00:00:00.000Z"
}
```

## Human-readable Markdown convention

```markdown
# P### — Project title

Status: Planned
Track: T### (or None)
Source: docs/field-guides/P###-Name.md

## Overview
Short summary.

## Milestones
- [ ] Milestone title

## Tasks
- [ ] Actionable task

## Notes
Dated decisions and observations.

## References
- [Source](relative/path)
```

Headings and prose remain valid documentation. The canonical JSON record is the machine-editable index; Markdown checklists remain supported as an interoperability format.

## Migration checklist for current SideQuests

1. Read the canonical registry and field guide for each P/T/E ID, plus linked notes, Brain Vault references and Change Journal corrections.
2. Reconcile duplicate/superseded IDs and distinguish plans from verified completions.
3. Produce a proposed JSON record with source paths and any ambiguity flagged. Do not infer completion, dates, or ownership.
4. Review each proposed record before writing to the Ledger. Retain all original Markdown and links.
5. Copy and verify destination; update the authoritative pointer only after successful verification.
6. Validate every ID, task count, milestone state, association, and source reference against the originals.

**Current status:** standard defined; the existing project inventory has not yet been bulk-converted or validated. The first automated migration path only covers a single structured JSON record, not all legacy documentation.
