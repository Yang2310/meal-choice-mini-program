# Product Features — Meal Choice & Food Library

**English** | [简体中文](features.zh-CN.md) · [Main README](../README.md)

This page describes the **implemented application workflows** unless explicitly marked as future work. The Mini Program is not publicly released.

## Eating out: single food or an entire store visit

- Save an individual food item with a store name, optional address, category, eaten/want-to-eat state, dining mode, rating where applicable, notes, tags, price, event date and optional image.
- Alternatively, **record several foods under one store** in a single workflow.
- Reuse known stores through autocomplete. Store identity and location are handled separately; a conflicting non-empty location is reported instead of silently replacing an existing value.
- Prevent duplicate foods within the relevant recording flow and preserve editable unsaved items during store-type selection.

## Store information and types

- Maintain store-level ratings, locations, details and dish lists.
- Hierarchical store types: fixed main categories plus selected built-in secondary categories, with optional user-created secondary types.
- Select up to three types and use them for store browsing and selection filters; types are not mandatory to record a dish.
- Store information and food-level properties remain separate.

## Cooking at home

- Manage cooked dishes and dishes planned for later.
- Cooking-specific categories, optional difficulty, cooking time, recipes, notes, tags and optional images.
- Independent cooking filters and random-choice pool.

## Library and random draw

- Browse by food or store, search, filter, edit, delete and review basic statistics.
- **Eating out — draw a dish:** status, category, dining mode, rating and selected-store filters.
- **Eating out — draw a store:** a separate store candidate pool, optional store-type filters and exclusions within the current session.
- **Cooking — draw a dish:** distinct candidate pool and filters.
- The reveal/animation presents the already chosen result; the selection is a business-logic decision, not an animation side effect.

## Identity, images and consistency

- WeChat silent sign-in; server-side code exchange, app-owned sessions and ownership checks.
- Per-item optional images, private Tencent COS storage, backend MIME and signature validation, signed detail URLs, replacement/deletion and rollback where needed.
- Store/food transactional creation and deletion semantics distinguish database success from incomplete external-media cleanup.
- Real WeChat text moderation is integrated in verified local WeChat-enabled flows.
- Asynchronous **image content moderation** has backend/client implementation and controlled tests; true production callback verification remains outstanding.

## Not implemented: screenshot-assisted data entry

The planned convenience feature would parse screenshots showing **orders, shops and dishes**, extracting the store name, location when present, purchased foods, quantities, prices and package information into editable form fields for user confirmation.

- Not an OCR/vision feature currently available in the Mini Program.
- Not an automatic classifier of the food photograph itself.
- Not a replacement for manual review or a means to invent missing data.
- No development deadline has been committed.

The manual input, store management, library and random-selection features are already functional without this addition.

## Deployment status

The private codebase includes local development/integration modes and a production deployment setup. Backend deployment/smoke testing has been documented, but this does not mean the WeChat Mini Program is already publicly released or its remaining production acceptance is complete.

[Status and limits](project-status.md)
