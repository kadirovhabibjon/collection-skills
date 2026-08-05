---
name: postman-collection
description: "Create or update Postman collections for API projects using a clean reusable structure with one request per endpoint, collection variables for baseUrl/auth/runtime values, no duplicate IP/host requests, optional postman/README.md usage notes, and validation by JSON parsing plus real request flow when the service is runnable. Use when asked to make Postman collections, prepare API collections for teams/leads, remove duplicate Postman requests, move Base URL into variables, or test API endpoints through Postman-style flows."
---

# Postman Collection

## Overview

Build Postman collections that teams can import directly from the codebase. Keep the collection small and reusable: one request per endpoint, no separate request copies for each IP or host.

Use `postman/` as the default project location:

```text
postman/
├── README.md
└── <service-name>.postman_collection.json
```

Do not add Postman environment JSON files unless the user explicitly asks for them.

## Workflow

1. Inspect the API surface from source code and docs:
   - FastAPI: route decorators and `/openapi.json` when available.
   - Spring: controller mappings.
   - Express/Nest/etc.: router definitions.
   - Existing `README.md`, `API.md`, Swagger/OpenAPI specs.
2. Create one Postman request per real endpoint or required workflow step.
3. Put shared values in collection variables:
   - `baseUrl`
   - auth values such as `apiKey`, `accessToken`, or `username/password`
   - workflow runtime values such as `fileKey`, `downloadUrl`, `entityId`
4. Use scripts to save runtime values from successful responses:
   - `pm.collectionVariables.set("fileKey", json.key)`
   - `pm.collectionVariables.set("downloadUrl", json.url)`
5. Add prerequest guards when a request depends on prior output:
   - fail early with a clear message when `fileKey`, `downloadUrl`, or another required variable is empty.
6. Add focused tests for status codes and required response fields.
7. Write `postman/README.md` with import steps and request order.
8. Validate JSON with `python -m json.tool`.
9. If the service is runnable, test the real request flow with curl or Newman if available.

## Collection Rules

- Keep endpoint requests unique. Do not create `Health localhost`, `Health 127`, `Health server`, etc.
- Test different IPs by changing the `baseUrl` collection variable.
- Prefer collection variables over Postman environment files for a self-contained collection.
- Keep error-case requests out unless the user asks for validation coverage.
- Do not store secrets from `.env` unless they are already safe local/demo values. If uncertain, use placeholders.
- Do not include generated presigned URLs, one-off upload responses, or temporary output files in the repo.

## Request Order Pattern

For upload/download services, use this order:

```text
Health Check
Upload File
Get Download Link
Download File Using Presigned URL
```

`Upload File` should save variables needed by later requests. Users should not have to manually copy `key` or `url` after a successful upload.

## Storage Service Pattern

When the project matches the Storage Service shape:

- `GET /healthz`
- `POST /api/v1/files`
- `GET /api/v1/files/link?key=...`
- presigned direct download URL

Use collection variables:

```text
baseUrl=http://localhost:<port>
apiKey=<configured API key>
expiresIn=3600
fileKey=
downloadUrl=
```

Use collection-level API key auth:

```text
X-API-Key: {{apiKey}}
```

Set `Health Check` and presigned download request to `noauth`.

## Validation Checklist

Before finishing:

- `postman/` contains only the collection JSON and optional `README.md`.
- Collection has no duplicate host/IP requests.
- `baseUrl` exists in collection variables.
- Auth variables are collection variables.
- Runtime variables are saved by scripts.
- Dependent requests have clear prerequest guards.
- JSON parses successfully.
- Real flow is tested when possible:
  - health returns `200`
  - upload returns `200`
  - link request returns `200`
  - presigned download returns `200`
