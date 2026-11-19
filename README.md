# Meal Choice & Food Library — WeChat Mini Program

**A usable full-stack personal food library and meal-decision app, built with a native WeChat Mini Program and a Node.js / Fastify backend.**

**Role:** Independent full-stack developer — product flow, data modeling, API integration, media security, testing and deployment preparation.  
**Stack:** JavaScript · WeChat Mini Program · Node.js / Fastify · MySQL · Tencent COS · WeChat APIs · Docker / Nginx  
**Release status:** **Core manual-entry workflows are functional; the Mini Program has not been publicly released.** This is not a publicly accessible live demo.

**English** | [简体中文](README.zh-CN.md)

## What the application does

The problem is familiar: people often forget what they have eaten or struggle to decide what to eat next. This project combines a personal food history with a configurable random-choice workflow.

Two distinct modes share one application:

- **Eating out:** record restaurants/stores and individual dishes; search the library by store or dish, edit ratings and details, and randomly draw a **dish or a store**.
- **Cooking at home:** maintain a separate library for dishes already cooked or planned, filter candidates, and randomly choose a home-cooking option.

**The main app is usable through manual entry today.** Entering store names, locations and many dishes by hand can be tedious. A planned screenshot-to-form feature addresses that usability gap, but **it has not been implemented and has no committed delivery date**.

## Product workflows already implemented

| Workflow | Functional scope |
| --- | --- |
| Food recording | Single-dish or multi-dish-at-one-store input; restaurant name, location, rating, food categories, tags, price, date, notes, optional picture |
| Store organization | Store search/autocomplete, location-conflict handling, editable store details, hierarchical store types and user-defined subtypes |
| Food library | Separate eating-out / home-cooking views; browse, search, filter, statistics, edit, delete |
| Meal selection | Separate randomized **store**, **dish** and **home-cooking** candidate pools; filters, candidate counts and reveal interaction |
| User and media | WeChat login, user-scoped data, private COS image storage, short-lived display URLs, upload/replace/delete with explicit cleanup handling |

The interface and core business flows have been developed and exercised. Completion of an interactive product flow is **not** the same as final production/WeChat release acceptance.

[Detailed feature breakdown](docs/features.md) · [Development and release boundary](docs/project-status.md)

## Architecture

The Mini Program uses repository and media-service interfaces so business screens do not have to embed cloud-specific behavior.

```text
WeChat Mini Program (JavaScript / WXML / WXSS)
  ├─ Pages: recording / library / store detail / random draw
  ├─ Business services and repository / media adapters
  └─ Authenticated HTTPS / REST requests
                  │
                  ▼
Node.js / Fastify API (/api/v1)
  ├─ Authentication and per-user ownership checks
  ├─ Store / food / filter / draw business operations
  ├─ MySQL transactions and versioned migrations
  ├─ Tencent COS private-media lifecycle
  └─ WeChat identity and content-moderation APIs
```

Development profiles support browser/WeChat local data, local API + MySQL, real WeChat identity integration and real COS media integration. The production target uses Docker/Nginx/HTTPS and a managed MySQL backend, with release verification handled separately.

[Architecture and engineering tradeoffs](docs/architecture.md)

## Engineering highlights

**1. Relational data modeling and consistency.** Stores, individual dishes and store types have separate identities and ownership rules. A store can hold multiple foods in one atomic creation operation; matching an existing store does not silently overwrite a conflicting non-empty location.

**2. Multiple selection engines.** Store drawing uses a type-aware candidate filter and excludes selected results within the draw session. Dish selection and home-cooking selection remain independent, preserving their own filters and rules.

**3. Secure image handling.** The client prepares images; Fastify validates MIME type and file signatures, controls object-key namespaces, verifies ownership and stores assets privately in Tencent COS. Detail pages obtain short-lived signed display URLs; list pages avoid generating them.

**4. Cross-system failure handling.** Database writes and COS deletion cannot form one atomic transaction. The implementation distinguishes complete versus partial deletion, and rolls back uploaded images when a subsequent record-creation operation fails.

**5. Real integrations and testable boundaries.** The project includes actual local integration with WeChat identity/text safety and COS, configurable local versus production adapters, automated frontend/backend tests, an API smoke suite and production-oriented container configuration. The asynchronous image-safety workflow has implementation and mocked callback tests, while **actual production HTTPS callback acceptance remains unverified**.

## Future enhancement — screenshot-based assisted entry

**Not implemented. No committed ETA. Not required for the existing manual-entry workflows to function.**

The intended experience is to upload a screenshot from a food order, takeaway, group-buy or similar page and **extract objective information visibly present** — for example:

- restaurant/store name and available address;
- dish/item names, quantities and listed prices;
- package or combo details when the screenshot makes them explicit.

The information would be used to **prefill an editable form**, with user review and confirmation before saving. The system should not invent missing addresses or auto-generate subjective ratings, store categories or food evaluations.

This is **document/screenshot understanding for data entry**, not automatic recognition of dishes from food photographs. It is also separate from **WeChat image content moderation**, which checks whether images are allowed.

## Where the project stands

**Available as an implemented application:** manual entry, personal library, store types and details, three selection modes, identity/data isolation, media flows and local testing. The private project documentation also records production-backend deployment and smoke testing.

**Not a published Mini Program:** public release, final device/domain/production checks and remaining acceptance are separate. Screenshot-based assisted entry is deferred for convenience rather than a prerequisite for the underlying food-library or draw logic.

This public repository is a technical showcase; it does not include a runnable copy of the private product, real user information, private assets or credentials.

[Read the concise project status](docs/project-status.md) · [中文版本](README.zh-CN.md)
