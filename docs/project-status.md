# Project Status

This repository is a **public portfolio showcase** for an actively developed full-stack application.

The full implementation is maintained in a separate private source repository.

## Current State

### Completed and exercised locally

- Core WeChat Mini Program product workflows
- Node.js / Fastify REST backend
- MySQL persistence and migrations
- Local API integration
- Real WeChat silent identity integration
- Application session/token flow
- Real WeChat text content moderation
- Private Tencent COS upload flow
- COS-backed detail display with signed URLs
- Image replacement and deletion
- Upload rollback when record creation fails
- Explicit complete / partial media cleanup reporting
- Automated frontend and backend tests
- Production Docker / Nginx skeleton

### In progress / not yet production-complete

- WeChat image content moderation
- Real TDSQL-C Serverless connection
- Tencent Lighthouse runtime deployment
- Production HTTPS verification
- WeChat production domain configuration
- Production device acceptance
- Local-data migration to the production backend
- Experience-version and release acceptance

## Next Development Sequence

### 1. Image moderation

Add the asynchronous image-safety workflow around already-uploaded COS assets:

```text
upload
→ pending asset
→ submit image check
→ verified callback
→ approved / rejected / error
```

The project already has moderation-oriented asset fields in its private schema, so this phase can build on the existing media model instead of replacing it.

### 2. Production data connection

Connect the existing MySQL repository and migrations to TDSQL-C Serverless over the intended private network path.

### 3. Lighthouse + HTTPS deployment

Deploy the containerized backend behind Nginx, then validate:

- `/health`
- `/ready`
- HTTPS
- authentication
- text moderation
- database access
- COS access

### 4. Production mini-program acceptance

Switch the mini-program to the production profile, configure legal domains, and test real-device flows end to end.

### 5. Data migration

Migrate any retained local data only after the production API and storage path have been validated.

### 6. Release preparation

Perform experience-version testing, error/recovery checks, privacy and user-data checks, and final release preparation.

## Documentation Note

The private project's README/progress documentation may temporarily lag behind manual integration testing. The public showcase should only be updated when a capability has been manually verified and is safe to describe without exposing credentials or user data.

## What Is Intentionally Not Public

This showcase does not contain:

- private application source code
- API or platform credentials
- database credentials or contents
- user data
- signed media URLs
- private infrastructure details

Its purpose is to communicate the product scope and engineering work without turning the private production repository into a public source release.
