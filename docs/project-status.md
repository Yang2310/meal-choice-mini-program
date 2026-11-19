# Project Status — Portfolio Milestones

**English** | [简体中文](project-status.zh-CN.md) · [Main README](../README.md)

**Functional core / Pre-release Mini Program.** The central product is implemented and usable through manual entry; there is **no public WeChat Mini Program release yet**.

This page distinguishes product functionality, documented integration evidence and final public release. It is not a daily change log.

## Implemented and usable

- Native Mini Program workflows for adding, viewing, editing and deleting eating-out and home-cooking foods.
- Single-food and multi-food-per-store recording; store autocomplete and location-conflict safeguards.
- Store metadata and hierarchical store-type selection, including user-defined secondary types.
- Food library, search/filter, statistics, store detail and three random-draw paths (store / eating-out dish / home-cooking dish).
- Node.js/Fastify versioned REST APIs, MySQL relations/migrations/transactions, application-owned sessions and user ownership boundaries.
- Real WeChat identity and text safety and private COS media integration exercised in local environments.
- Backend/frontend automated tests, local API smoke tests, deployment configuration and documented backend deployment/smoke evidence.

## What is separate from completion of the core product

**Screenshot-assisted form filling — not implemented, no committed schedule.** Future intent: interpret screenshots of food orders / group-buy pages to prefill store name/address, foods, quantities and prices when visible, followed by manual review. This is an experience improvement, not a claim of present AI functionality.

**WeChat image content moderation — different feature.** The asynchronous moderation submission, trace/callback matching, state handling and related test code exist. Real public HTTPS callback acceptance is not recorded as complete.

**Public release and remaining validation.** The Mini Program has not been published to general users. Backend deployment/smoke is documented, but that cannot by itself establish the current state of every cloud component, database migration, legal domain, production security check or device acceptance. These require independent verification before claiming an official release.

## What a recruiter can assess now

- A practical multi-entity product and complete manual input-to-selection user journeys.
- Database/API design, per-user authorization, storage integrations and consistency failure handling.
- Configurable environment boundaries and tests.
- Clear separation between *working product functionality*, *deployment evidence* and *future intelligence features*.

## Public-sharing boundary

The application implementation remains private. This repository publishes architecture, engineering decisions and product scope only; it excludes user records, screenshots containing personal information, private images, source credentials, cloud secrets and production configuration.

Update this document at meaningful product or release milestones, not every small code change.
