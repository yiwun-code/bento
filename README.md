# 生根有機農場 訂餐系統 — Shengen Organic Farm Ordering System

A mobile-first, bilingual (繁中 / EN) pre-order web app built for a real organic farm meal delivery service in Taipei. Customers place daily lunch orders; the farm owner manages the menu, pickup locations, pricing, and order fulfilment through a built-in admin panel.

**Live site:** https://yiwun-code.github.io/bento

---

## Tech Stack

| Layer | Choice | Why |
|---|---|---|
| Frontend | Vanilla HTML/CSS/JS (single file) | Zero build step, instant deploy via GitHub Pages, easy for non-technical owner to host |
| Backend / DB | Supabase (PostgreSQL + REST) | Managed auth, RLS for data access control, real-time capable, generous free tier |
| Auth | Supabase email auth | Role-based: boss vs. helper, different admin views |
| Hosting | GitHub Pages | Free, no server to manage |

---

## Features

### Customer-facing
- Date and pickup location selection (location availability varies by date)
- Menu grouped by type: 便當 (bento) / 沙拉 (salad) / 單點 À la carte (available as standalone items or add-ons alongside a main)
- Per-item options: with/without carb, sauce selection
- Bulk discount automatically applied when ordering 3+ items
- Bilingual UI — switch between 中文 and English mid-flow
- Add-to-calendar button on the order confirmation page

### Admin panel
- Role-based access: boss sees all settings, helper sees orders only
- Live order dashboard with revenue summary and item breakdown
- Menu editor: add/remove/toggle items, set Chinese + English names, tags, price
- Location & pricing editor: per-location price overrides, discount thresholds, service hours, pause dates
- Taco flavor management: view how many orders each flavor has received per date; manually lock/unlock daily flavor availability

---

## Interesting Design Decisions

**Single-file architecture**
The entire app — ~3,000 lines — lives in one `index.html`. This is a deliberate tradeoff: the owner can open, inspect, and self-host the file without any build tooling. Supabase handles all persistent state, so the file itself is stateless.

**Optimistic rendering with background sync**
On load, the app renders immediately from `localStorage` cache (menu, locations, settings), then silently fetches fresh data from Supabase in the background and patches the UI. This avoids a loading spinner on every visit while still staying up to date.

**Taco flavor daily pool logic**
Mexican tortilla items share a daily flavor pool. Once 3 distinct flavors accumulate enough orders, the system locks them as the day's selection and greys out the rest — not with a generic "sold out" badge, but with two distinct states: *"今日不供應 / Not available today"* (flavor not in the day's pool) vs *"已選 3 種 / 3 flavors selected"* (personal per-order limit reached). This distinction prevents customer confusion.

**i18n without a framework**
All static strings use `data-i18n` attributes resolved by a `T` translation object and `applyI18n()`. Dynamic JS-rendered strings use a `t(key)` helper. A `DEFAULT_ITEM_EN` / `DEFAULT_LOC_EN` lookup table provides English names as fallback for existing data that predates the EN fields, so no manual migration was needed.

**Location-aware pricing**
Each pickup location can have its own price tier, discount threshold, and available dates. The menu renders prices dynamically based on the selected location, and the date picker only shows dates that location is active.

---

## Screenshots

> Add screenshots to a `/screenshots` folder and update the paths below.

| Customer ordering flow | Admin dashboard | Bilingual UI (中 / EN) |
|---|---|---|
| ![ordering flow](screenshots/ordering.png) | ![admin](screenshots/admin.png) | ![bilingual](screenshots/bilingual.png) |

---

## Project Structure

```
index.html        # Entire app — frontend + admin panel
README.md
```

All persistent data (orders, menu, locations, settings) lives in Supabase. `localStorage` is used only as a render cache and for language preference.

---

## About

Built for 生根有機農場, a small organic farm operating lunch delivery routes across Taipei. The system replaced a manual LINE-based ordering process.
