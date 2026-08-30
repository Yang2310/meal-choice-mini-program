# Meal Choice & Food Library Mini Program

A full-stack WeChat Mini Program for recording personal food choices and randomly deciding what to eat next.

> **Status:** In active development  
> **Role:** Independent full-stack developer  
> **Focus:** WeChat Mini Program · Node.js / Fastify REST API · MySQL · Tencent COS · WeChat authentication & content safety · containerized deployment

**English** | [简体中文](README.zh-CN.md)

## Project Overview

This project is a personal food-library and meal-decision application built as a native WeChat Mini Program with a self-hosted backend.

It supports two related workflows:

- **Eating out:** record stores and dishes, including ratings, categories, dining modes, tags, notes, price, date, and an optional image.
- **Cooking at home:** record dishes already cooked or planned for later, then randomly generate a choice when deciding what to make.

The product is designed around a simple loop:

```text
Record food
    ↓
Organize personal library
    ↓
Filter candidates
    ↓
Randomly select a dish or store
    ↓
Reveal the result through a lightweight blind-box interaction
```

The private source repository contains the full implementation. This public repository is a portfolio showcase focused on product capabilities, architecture, engineering decisions, and development status.

## What I Built

- A native WeChat Mini Program frontend using JavaScript, WXML, and WXSS
- A Node.js / Fastify backend exposing versioned REST APIs
- A MySQL persistence layer with versioned, non-destructive migrations
- Store and food-item relationships with transactional batch creation
- Search, filtering, library statistics, editing, deletion, and random selection
- Separate meal-selection logic for eating out and cooking at home
- WeChat silent authentication with application-owned opaque sessions
- Real WeChat text content-safety integration
- Private Tencent COS image storage with authenticated upload and deletion
- Short-lived signed image URLs generated only when detail pages need them
- Per-user ownership checks for data and media
- Environment-specific adapters for local development, API integration, WeChat integration, COS integration, and production
- Docker and Nginx production deployment preparation
- Automated backend and mini-program tests plus local API smoke testing

## Product Workflow

```text
┌──────────────────────────────┐
│      WeChat Mini Program     │
│ record · search · filter     │
│ random pick · detail/edit    │
└───────────────┬──────────────┘
                │ REST / HTTPS
                ▼
┌──────────────────────────────┐
│       Node.js / Fastify      │
│ auth · validation · services │
│ business rules · API routes  │
└───────┬──────────┬───────────┘
        │          │
        ▼          ▼
┌──────────────┐  ┌────────────────┐
│    MySQL     │  │  Tencent COS   │
│ structured   │  │ private images │
│ application  │  │ signed access  │
│ data         │  │                │
└──────────────┘  └────────────────┘
        │
        ▼
┌──────────────────────────────┐
│        WeChat APIs           │
│ login · text content safety  │
└──────────────────────────────┘
```

More detail: [docs/architecture.md](docs/architecture.md)

## Key Capabilities

### Personal Food Library

Users can record food from restaurants or home cooking, organize entries with fixed categories and custom tags, and search or filter the library later.

### Store & Dish Relationship

Eating-out records are modeled as stores containing individual food items. Users can add one dish at a time or record several dishes from the same store in one flow.

### Random Meal Selection

The app can randomly choose from filtered candidates instead of asking the user to compare choices manually. Eating-out and home-cooking candidates remain separate.

### Private Image Storage

Each food item can have an optional image. Images are compressed on the mini-program side, uploaded through the authenticated backend, stored privately in Tencent COS, and exposed to detail pages through short-lived signed URLs.

List pages intentionally do not request image URLs, reducing unnecessary object-storage traffic.

### Authentication & User Isolation

The mini-program uses `wx.login`, while the backend exchanges the temporary code and issues its own opaque session token. Business data and media operations are scoped to the authenticated internal user.

### Content Safety

User-entered text is checked through WeChat's content-safety API in WeChat-enabled environments. Text is validated before business data is written.

Image content moderation is the next moderation phase and is not presented as complete in this showcase.

## Tech Stack

**Frontend**
- WeChat Mini Program
- JavaScript
- WXML / WXSS

**Backend**
- Node.js 20+
- Fastify
- REST API
- `@fastify/multipart`

**Data & Storage**
- MySQL 8
- Tencent COS
- Versioned SQL migrations

**External Integration**
- WeChat login / code exchange
- WeChat text content-safety API

**Deployment**
- Docker
- Nginx
- HTTPS production architecture
- TDSQL-C Serverless target database

## Engineering Highlights

The project uses explicit boundaries instead of embedding cloud-specific logic directly in pages:

```text
Mini Program Page
      ↓
Business Service
      ↓
Repository / Media Service
      ↓
API Adapter
      ↓
Backend
      ↓
DataRepository · MediaStorage · IdentityProvider · ContentModerator
```

This allows local development and production-oriented integrations to share the same product workflows while swapping infrastructure through configuration.

Other engineering decisions include:

- production configuration rejects unsafe local/mock adapters
- media object keys are generated by the backend under a project-specific namespace
- clients cannot choose COS object keys
- media ownership is checked server-side
- image type is validated using both declared MIME and file signatures
- database metadata is kept separate from signed display URLs
- food deletion and COS cleanup report complete vs partial outcomes explicitly
- failed create workflows roll back already-uploaded media
- store records are soft-deleted when their final active food item is removed
- list endpoints avoid generating image URLs

## Current Development Status

The core application, local API integration, real WeChat identity flow, real WeChat text moderation, and real COS media workflow have been exercised in localhost integration.

The next major work is:

1. WeChat image content moderation
2. Real TDSQL-C connection
3. Lighthouse + Nginx + HTTPS deployment
4. Production WeChat / domain validation and device acceptance
5. Local-to-production data migration
6. Experience-version testing and release preparation

See [docs/project-status.md](docs/project-status.md).

## Repository Structure

```text
.
├── README.md
├── README.zh-CN.md
└── docs/
    ├── architecture.md
    ├── features.md
    └── project-status.md
```

## Public Showcase Scope

This repository intentionally does **not** contain:

- private production source code
- WeChat AppSecret or application credentials
- Tencent COS credentials
- database credentials or contents
- user data
- signed media URLs
- private infrastructure configuration

It is maintained as a portfolio repository that documents the project without exposing the private implementation.
