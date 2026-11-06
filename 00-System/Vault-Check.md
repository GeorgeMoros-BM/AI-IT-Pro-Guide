---
title: "Vault Check"
artifact_type: operational-system
status: draft
last_updated: 2026-11-05
publish: false
client_safe: true
---
The vault check finds broken links and metadata problems before they spread. It runs on GitHub once a day and on demand.

It uses a baseline of known issues and fails only when a new issue appears. The vault can improve over time without having to be perfect first.

---
# Setup

The `.github` folder has to be added by hand, because remote tools can't write to it. Extract `vault-check-setup.zip` into the vault root. Until `.github/workflows/vault-check.yml` exists there, the check isn't running.

---
# What It Checks

These fail the check when new:
- links to notes that don't exist
- links into `99-Archived`
- missing fields from [[Metadata-Standards]]
- artifact types not approved in [[Metadata-Standards]]
- statuses not listed in [[Lifecycle-States]]
- two notes with the same name
- likely sync or conflict copies, such as `Note-1.md`
- empty notes
- `publish: true` without `client_safe: true` and `status: publish-ready`

These are warnings only:
- dead links in `11-Research`, because weekly reports are dated records
- canonical notes not updated in 180 days

Weekly reports and the `Templates`, `90-Local-Admin`, and `99-Archived` folders skip the metadata checks.

---
# How It Runs

- **Schedule:** Once a day. Obsidian Git pushes every five minutes, so a check on each push would send a failure email for every link still being written.
- **On demand:** GitHub > Actions > Vault check > Run workflow.
- **Scope:** Only what Git tracks. `Templates`, `90-Local-Admin`, and `99-Archived` are git-ignored, so a link to a note in those folders shows as unresolved.

---
# When It Fails

1. Open the failed run on GitHub. The summary lists each new issue.
2. Fix the issue, or accept it if it's intentional.
3. To accept the current state, run the workflow with `update_baseline` ticked. This also locks in anything you've fixed.

The baseline is `.github/vault-check-baseline.json`. The script is `.github/scripts/vault_check.py`.

---
# Governance Rule

Fix issues where you can. Accept an issue only when it's intentional, such as a link to a note you plan to write soon.
