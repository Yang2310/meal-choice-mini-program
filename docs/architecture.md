# Architecture & Engineering Decisions

**English** | [简体中文](architecture.zh-CN.md) · [Main README](../README.md)

## 1. Core separation

The native WeChat Mini Program provides the interaction layer. Its pages depend on business services, Repository and MediaService interfaces, not directly on server-side MySQL/COS internals.

```text
Mini Program pages (record / library / store details / draw)
              ↓
Business services (food / store / filters / drawing)
              ↓
Repository & MediaService adapters
              ↓ HTTPS / JSON + authenticated uploads
Node.js / Fastify backend (/api/v1)
   ├─ Identity / authorization / validation
   ├─ MySQL repositories / migrations / transactions
   ├─ Tencent COS private-media lifecycle
   └─ WeChat identity and content-safety integrations
```

Supported environments differentiate local-only data, local API + MySQL, WeChat identity/text safety, COS-backed media and the production configuration. These paths share application behavior while keeping infrastructure choices explicit.

## 2. Food versus store identity

A **Store** owns its name, location, overall rating and optional store types. A **Food** entry has its own status, tags, categories, rating, price, date and media, linked to a store for eating-out records.

Normalized store name is the current identity rule **within one user**. A conflicting address is not silently overwritten. Store types are optional and modeled separately from food categories, so choosing a type does not block quick food recording.

The same store can have multiple food entries, and an atomic multi-food recording operation prevents partially created store visits.

## 3. Filtering and independent draw engines

The app separates choosing an **eating-out dish**, an **eating-out store**, and **a home-cooked dish**. They have different candidate filters and must not share state accidentally.

Store drawing uses store-type hierarchy and session exclusions; the client keeps the reveal interaction separate from the actual random selection. This makes business tests independent of animation.

## 4. Identity and private media

```text
wx.login → server code exchange → internal user ID
   → app-owned opaque session → authorized API
   → backend-checked food/store/media ownership
```

For optional food images:

```text
Client image preparation
   → authenticated multipart upload
   → MIME + file-signature validation
   → backend-generated COS object key
   → private Tencent COS bucket
   → database metadata
   → signed short-lived detail URL (on demand)
```

Regular list endpoints do not need fresh signed media URLs. The server owns object-key generation and upload/delete permission checks.

## 5. Failure and transaction boundaries

MySQL and COS cannot participate in one ACID transaction. A multi-entity create uses MySQL transaction semantics; uploaded media is cleaned up when a downstream create fails.

For deletion, the API distinguishes full success from **partial cleanup** (the database operation succeeded but COS cleanup failed) and failure of the database operation. The UI should not claim all cloud data was removed merely because a database row was deleted.

## 6. Screenshot recognition versus moderation

**Future, not implemented:** parse a user's screenshot of an order or deal page to extract objective store/address/dish/quantity/price information and offer an editable prefilled form. The user confirms the details before persistence. No vision-model provider or completion date is being claimed.

**Separate existing engineering component:** WeChat image **content-safety** handling includes asynchronous submission, state tracking, signed callback handling and automated tests. This controls whether uploaded images may be shown, not what restaurant/dish they depict. Production callback acceptance remains outstanding.

## 7. Deployment and verification

The production-oriented design uses a self-hosted Node/Fastify backend behind Nginx/HTTPS and managed MySQL plus COS. Development includes local integration with real WeChat identity and media APIs.

The private documentation records backend deployment and smoke testing. **The overall WeChat Mini Program is not publicly released**, and cloud/HTTPS/domain/device acceptance must not be conflated with source implementation or isolated smoke tests.

This portfolio deliberately omits infrastructure credentials, user data, signed URLs and private application code.
