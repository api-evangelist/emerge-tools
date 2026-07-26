---
name: Review snapshot differences in a PR
description: Fetch snapshot comparison data between two builds and resolve it by ignoring diffs or skipping the check.
api: openapi/emerge-tools-openapi.json
operations:
  - "GET /v2/snapshots/diff"
  - "PUT /v2/snapshots/ignore"
  - "POST /v1/snapshots/skip"
generated: '2026-07-19'
method: generated
source: openapi/emerge-tools-openapi.json, https://docs.emergetools.com/docs/snapshot-testing
---

# Review snapshot differences in a PR

Drive Emerge Snapshots visual-regression review over the API. Base URL `https://api.emergetools.com/`; authenticate with the `X-API-Token` header.

## Steps

1. **Get the comparison** — `GET /v2/snapshots/diff` for the head/base builds. Returns added, removed, changed, and unchanged snapshots (or single-build data when no base is given).
2. **Triage each changed snapshot** — for an intentional change, `PUT /v2/snapshots/ignore` to mark that specific snapshot difference ignored (or unignored). Ignored diffs stop triggering alerts and are not counted as changes.
3. **Skip the check when irrelevant** — for a PR with no snapshot-relevant changes (common in monorepos where the check is required), `POST /v1/snapshots/skip` with the commit `sha` to post a successful, skipped Emerge Snapshots GitHub check so the PR can merge.

## Rules

- Treat `400` as a malformed request (bad sha/params) and `404` as "no comparison data for those builds" (see `errors/emerge-tools-problem-types.yml`).
- Ignoring a diff is scoped to a single snapshot difference, not the whole build — iterate over the changed set.
- Skipping the check is an explicit human/agent override; record why, since it bypasses the visual gate.
