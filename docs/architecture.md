# Architecture

## Overview

The application is split into a native WeChat Mini Program and a self-hosted Node.js backend.

```text
WeChat Mini Program
        ↓
Page / interaction layer
        ↓
Business services
        ↓
Repository + MediaService
        ↓
Authenticated REST API
        ↓
Node.js / Fastify backend
        ↓
┌─────────────────┬──────────────────┬─────────────────────┐
│ DataRepository  │ MediaStorage     │ Identity / Moderation│
│ MySQL           │ Tencent COS      │ WeChat APIs          │
└─────────────────┴──────────────────┴─────────────────────┘
```

## Frontend Boundary

The mini-program UI does not directly connect to MySQL, Tencent COS, or WeChat server-side APIs.

Application pages depend on abstractions that can be configured for several environments:

- `local`
- `localApi`
- `wechatApi`
- `wechatMediaApi`
- `production`

This keeps normal UI and business workflows independent from the infrastructure used underneath.

## Backend Boundary

The backend exposes versioned `/api/v1` routes and separates infrastructure into stable responsibilities:

### DataRepository

Responsible for:

- stores
- food items
- tags and categories
- user settings
- random-selection candidates
- assets metadata
- transactions and soft deletion

Local development and production use the same MySQL repository implementation with different connection settings.

### MediaStorage

Responsible for:

- uploading private media
- deleting objects
- generating short-lived display URLs

The current production-oriented adapter uses Tencent COS.

The database stores media metadata and stable object keys. Signed URLs are generated only when needed and are not persisted.

### Identity

The WeChat-enabled flow is:

```text
wx.login
   ↓
temporary code
   ↓
backend code exchange
   ↓
internal user mapping
   ↓
application-owned opaque session token
   ↓
Bearer-authenticated business API
```

The mini-program does not store WeChat AppSecret or server-side identity secrets.

### Content Moderation

User-entered text is sent through the WeChat content-safety API before business data is written in WeChat-enabled environments.

Image moderation is intentionally a separate upcoming phase because it requires asynchronous moderation state and callback handling.

## Media Flow

```text
Select image
    ↓
Client-side size/dimension preparation
    ↓
Authenticated multipart upload
    ↓
Backend MIME + file-signature validation
    ↓
Backend-generated project/user object key
    ↓
Private Tencent COS object
    ↓
Asset metadata in MySQL
```

Important rules:

- one active image per food item
- the client cannot supply the COS object key
- object keys stay inside a project namespace
- owner checks are enforced on asset operations
- list endpoints do not generate signed URLs
- detail endpoints generate short-lived signed URLs only when needed
- uploaded media is rolled back when the later create workflow fails

## Delete & Consistency Semantics

MySQL and object storage cannot participate in one atomic transaction.

The application therefore distinguishes:

- **success:** database deletion completed and all related COS cleanup completed
- **partial:** database deletion completed but one or more COS cleanup operations failed
- **failure:** the database operation itself failed

This prevents the UI from claiming a complete deletion when cloud-media cleanup was incomplete.

For a standalone “remove image but keep food item” operation, COS deletion remains strict: metadata is only marked deleted after object deletion succeeds.

## Production Target

```text
WeChat Mini Program
        ↓ HTTPS
Nginx on Tencent Lighthouse
        ↓ localhost container port
Node.js / Fastify Docker container
        ↓ VPC
TDSQL-C Serverless MySQL

Node backend
        ↓
Tencent COS

Node backend
        ↓
WeChat identity / moderation APIs
```

The repository already contains the deployment skeleton, but real production deployment and acceptance are still in progress.
