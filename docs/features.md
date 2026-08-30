# Features

## Eating Out

- Record a single dish
- Record multiple dishes from one store
- Store-level information and rating
- Dish categories
- Eaten / want-to-eat state
- 1–5 score for eaten dishes
- Dine-in / delivery mode
- Custom tags
- Notes
- Price and event date
- Optional image

## Cooking at Home

- Record dishes already cooked
- Record dishes to cook later
- Separate cooking categories
- Cooking difficulty
- Custom tags and notes
- Cooking time and recipe
- Optional image
- Independent random-selection candidate pool

## Personal Food Library

- Search food records
- Browse by food item or store
- Filter the library
- View store detail and its dishes
- Edit existing records
- Delete individual or multiple records
- Automatically remove empty stores after their final active dish is deleted
- Show summary statistics

## Random Selection

The app supports separate random flows rather than mixing all records together.

### Eating Out

Users can select filtered candidate ranges and randomly choose:

- a food item
- a store

### Cooking

Home-cooking records use an independent candidate pool and selection flow.

The blind-box animation reveals a result that has already been selected by the business logic; the animation itself does not determine the random outcome.

## Images

- At most one active image per food item
- JPEG / PNG / WebP
- Client-side image preparation before upload
- Authenticated backend upload
- Backend MIME and file-signature validation
- Private Tencent COS storage
- Project-specific object-key namespace
- User ownership validation
- Short-lived signed URLs for detail display
- No image requests from normal library lists
- Replace and delete support
- Rollback if media upload succeeds but later record creation fails

## Authentication

- Silent `wx.login` flow
- Server-side WeChat code exchange
- Internal user identity
- Application-owned opaque session token
- Hashed session persistence
- Bearer-authenticated API requests
- One-time 401 recovery on the mini-program side

## Content Safety

Implemented:

- WeChat text content-safety checks
- token caching and refresh behavior
- fail-closed business writes when moderation is unavailable or rejects content

Planned next:

- asynchronous WeChat image moderation
- moderation callback verification
- pending / approved / rejected / error media states

## Development & Deployment

- Local-only development mode
- Local API + MySQL mode
- Real WeChat identity integration mode
- Real COS integration mode
- Production configuration boundary
- Docker backend image
- Nginx HTTPS reverse-proxy template
- health and readiness endpoints
- automated backend and mini-program tests
- local API smoke test
