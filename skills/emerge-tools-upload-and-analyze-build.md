---
name: Upload a build and read its size analysis
description: Upload an iOS/Android build to Emerge Tools and retrieve its app-size + PR analysis results.
api: openapi/emerge-tools-openapi.json
operations:
  - "POST /upload"
  - "POST /uploadFromLink"
  - "GET /analysis"
  - "GET /appHistory"
  - "GET /buildDetails"
generated: '2026-07-19'
method: generated
source: openapi/emerge-tools-openapi.json, https://docs.emergetools.com/docs/rest-api
---

# Upload a build and read its size analysis

Use the Emerge API to upload a build and pull its size / PR analysis. Base URL `https://api.emergetools.com/`. Authenticate every request with the `X-API-Token` header (see `authentication/emerge-tools-authentication.yml`).

## Steps

1. **Request a signed upload URL** — `POST /upload` with the build metadata (`sha`, `branch`, `prNumber`, `baseSha`, `previousSha`, `buildType`, `filename`). The response contains `uploadURL` (and `upload_id`).
2. **Upload the artifact** — `PUT` the build file to the returned `uploadURL` with `Content-Type: application/zip` for `.zip` archives. (Alternatively skip steps 1–2 and use `POST /uploadFromLink` to have Emerge pull the artifact from a download link.)
3. **Retrieve analysis** — `GET /analysis?sha=<sha>` (or `?emergeId=<id>`). Prefer this over `/comment`: it returns a 200 while jobs are still in progress and indicates which are pending, rather than a 4xx. Specify `baseSha`/`baseEmergeId` to control the comparison base.
4. **(Optional) List recent builds** — `GET /appHistory` returns up to the 10 most recent builds; `GET /buildDetails` returns the per-module size breakdown for one build.

## Rules

- Address builds by Git `sha` or Emerge-assigned `emergeId`; comparisons are head-vs-base (see `conventions/emerge-tools-conventions.yml`).
- There is no idempotency-key header; build identity is `(sha, buildType)`. Re-uploading the same sha replaces prior data for that build.
- Handle `404` from `/analysis` as "build/analysis not found" (see `errors/emerge-tools-problem-types.yml`).
