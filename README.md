# GrandHaven Hotels — Functional Hotel Booking Platform

**CWI Task 39** — A complete, fully functional Hilton Hotels-inspired hotel booking platform. Search hotels and rooms, filter and sort results, view rich room details, book a stay, and have every booking persisted to a real database with a unique confirmation number.

> **This is not a landing page or UI mockup.** Every feature — search, filters, sorting, room details, image gallery, booking, database persistence, confirmation, and My Bookings — is fully functional end-to-end.

---

## ✨ Features

- **Hotel search** — search by destination, check-in, check-out, and number of guests, with full validation.
- **Dynamic results** — rooms are served from a live SQLite database with images, descriptions, pricing, ratings, reviews, amenities, and availability.
- **Functional filters** — price range (min/max), hotel rating (3+ / 4+ / 5+), and amenities (WiFi, Pool, Gym, Breakfast, and more) that genuinely change the displayed results.
- **Functional sorting** — Recommended, Price: Low to High, Price: High to Low, Best Rated.
- **Room details page** — large image gallery with lightbox, room facts (bed type, capacity, size, bathroom, view), room & hotel amenities, policies, and guest reviews.
- **Complete booking flow** — select dates and guests, enter guest details, review a dynamically calculated summary (nights, base price, taxes, fees, total), and confirm.
- **Real database persistence** — bookings, hotels, and rooms live in a real database: **Supabase (Postgres)** for production, or SQLite for local mode. Every booking gets a unique confirmation number (`HIL-2026-XXXXXX`), full price breakdown, and guest information.
- **Confirmation popup** — "Booking Confirmed! 🎉" appears only after the database save succeeds; failures show a "Booking Failed" dialog with a Try Again action.
- **My Bookings page** — every saved booking remains available with its confirmation number, dates, guests, nights, and total.
- **Secondary pages** — Deals (live discounts from the DB), Destinations (derived from the DB), Support (FAQs + contact form), and hotel detail pages.
- **Responsive design** — desktop, tablet, and mobile (320px → 1440px+) with a working hamburger menu, drawer filters, stacked forms, and no horizontal scrolling.
- **Loading & error states** — skeleton loaders, spinners, friendly error messages, and a global error boundary (never a blank page).
- **Accessibility** — semantic HTML, labelled inputs, keyboard navigation, focus states, and `aria` attributes throughout.

---

## 🛠 Technologies

| Layer      | Technology |
|------------|------------|
| Frontend   | React 18, React Router 6, Vite |
| Backend    | Node.js + Express (local mode) |
| Database   | **Supabase (Postgres)** in production — or SQLite via Node's built-in `node:sqlite` for local mode |
| Styling    | Hand-written CSS with a premium Hilton-inspired design system |
| Fonts      | Playfair Display + Inter (Google Fonts, with system fallbacks) |
| Imagery    | Free-to-use Unsplash CDN photos with automatic seeded fallbacks |
| Hosting    | Cloudflare Pages (static frontend + Supabase database) |

---

## 📁 Project Structure

```
.
├── server/                    # Express + SQLite backend (local mode)
│   ├── index.js               # Server entry: API routes + static SPA serving
│   ├── db.js                  # Database schema, seeding, helpers
│   ├── seed.js                # Dynamic hotel / room / review dataset
│   └── routes/
│       ├── hotels.js          # Hotels, hotel detail, destinations, deals
│       ├── rooms.js           # Room search (filters, sort, availability), room detail
│       └── bookings.js        # Booking create/list, search logging, pricing
├── src/                       # React frontend
│   ├── main.jsx               # App bootstrap
│   ├── App.jsx                # Routes + layout
│   ├── api.js                 # Data layer: Supabase when configured, Express fallback
│   ├── supabase.js            # Supabase client (activates on VITE_SUPABASE_*)
│   ├── utils.js               # Date / price helpers
│   ├── styles.css             # Complete design system (responsive)
│   ├── components/            # Header, Footer, HotelSearch, RoomCard, RoomGallery,
│   │                          # HotelFilters, HotelSort, BookingForm, BookingSummary,
│   │                          # BookingConfirmation, LoadingState, ErrorState, etc.
│   └── pages/                 # Home, SearchResults, RoomDetails, Booking, MyBookings,
│                              # HotelDetail, Deals, Destinations, Support, NotFound
├── supabase/                  # Supabase SQL files
│   ├── schema.sql             # Paste into the SQL editor: tables, views, RPC, RLS, grants
│   └── seed.sql               # Paste into the SQL editor: 18 destinations / 261 rooms
├── scripts/                   # Utility scripts
│   ├── generate-seed-sql.mjs  # Regenerates supabase/seed.sql from server/seed.js
│   └── seed-supabase.mjs      # REST alternative to seeding (needs service_role key)
├── public/
│   └── _redirects             # Cloudflare Pages SPA fallback
├── data/                      # SQLite database file (local mode, auto-created + seeded)
├── dist/                      # Production build output
├── index.html
├── vite.config.js
├── wrangler.toml              # Cloudflare Pages config
├── package.json
└── .env.example
```

---

## 🚀 Installation

Requirements: **Node.js ≥ 22.5** (uses the built-in `node:sqlite` module — no native compilation).

```bash
# 1. Clone and enter the project
git clone <your-repo-url> grandhaven
cd grandhaven

# 2. Install dependencies
npm install

# 3. Run in development (API on :4000, Vite dev server on :5173)
npm run dev
```

Open **http://localhost:5173**. The database is created and seeded automatically on the first server start.

### Production mode

```bash
# Build the frontend, then serve everything from the Express server
npm run build
npm start        # serves the app + API on http://localhost:4000
```

---

## 🔐 Environment Variables

Copy `.env.example` to `.env` if you want to override defaults. In **local mode** (no Supabase vars) no external services are needed — the app runs fully offline against SQLite.

| Variable | Default | Description |
|----------|---------|-------------|
| `API_PORT` | `4000` | Port the Express API/server listens on (local mode) |
| `DB_PATH` | `data/grandhaven.db` | Path to the SQLite database file (local mode) |
| `VITE_SUPABASE_URL` | — | Supabase project URL, e.g. `https://xxxx.supabase.co`. Setting this (+ anon key) switches the app to Supabase mode |
| `VITE_SUPABASE_ANON_KEY` | — | Supabase **anon / publishable** key (safe for the browser, required by Cloudflare Pages) |
| `SUPABASE_SERVICE_ROLE_KEY` | — | Supabase **service_role** key — NEVER expose in the browser; only used by `scripts/seed-supabase.mjs` |

---

## 🗄 Supabase Setup (production database)

The app ships with a ready-made Supabase schema. Setup takes about five minutes:

1. **Create a project** at [supabase.com](https://supabase.com) (free tier is fine).
2. **Run the schema** — open **SQL Editor → New query**, paste the entire contents of **`supabase/schema.sql`**, and click **Run**. This creates the tables (`hotels`, `rooms`, `reviews`, `searches`, `bookings`), read-friendly views, the `create_booking()` RPC (all validation + pricing + confirmation-number generation happens inside the database), row-level security policies, and grants.
3. **Seed the data** — one of two ways:
   - **Paste method (recommended for you):** open **SQL Editor → New query**, paste **`supabase/seed.sql`**, and click **Run**. It inserts 18 destinations / 261 rooms / 328 reviews and is safe to re-run.
   - **Script method:** `set SUPABASE_URL=...` and `set SUPABASE_SERVICE_ROLE_KEY=...`, then `node scripts/seed-supabase.mjs`.
4. **Copy the keys** — Dashboard → **Project Settings → API**: copy the **Project URL**, the **anon / publishable key**, and (for seeding only) the **service_role** key.

> **How it works:** when `VITE_SUPABASE_URL` + `VITE_SUPABASE_ANON_KEY` are present at build time, `src/api.js` talks to Supabase directly (reads from the views; bookings via the `create_booking` RPC). Without them it falls back to the local Express + SQLite API — so local development keeps working with no account.

---

## 🗄 Database

**Supabase (Postgres)** in production — full schema in `supabase/schema.sql` with views, a `create_booking()` RPC, and row-level security. **SQLite** (via `node:sqlite`) stores the same model locally in `data/grandhaven.db` for development. Tables:

- **`hotels`** — name, city, country, address, description, star rating, guest rating, review count, cover image, facilities (JSON).
- **`rooms`** — hotel reference, room name, description, price per night, discount %, guest capacity, bed type/count, room size, bathroom info, view type, amenities (JSON), images (JSON), availability.
- **`reviews`** — author, rating, comment, date, linked to hotels/rooms.
- **`searches`** — destination, check-in, check-out, guests, result count, timestamp (every search is logged).
- **`bookings`** — confirmation number (unique), hotel/room ids + names, guest name/email/phone, destination, dates, nights, guests, price per night, base price, taxes, fees, total, special requests, status, created timestamp.

### Hotel data
Each hotel record includes name, location (city/country/address), description, star & guest ratings, review count, facilities, and images — all read from the database at runtime.

### Room data
Each room includes a name, description, price per night, discount, guest capacity, bed type and count, room size, bathroom info, view type, amenities, images, and availability — all read from the database at runtime.

### Seeding
On first boot the server seeds **18 resort destinations** (Bali, Bangkok, Barcelona, Cancún, Cape Town, Dubai, Istanbul, Las Vegas, London, Malé, Miami, New York, Paris, Rome, Santorini, Singapore, Sydney, Tokyo) with **14–15 bookable rooms per destination** (261 rooms total) and hundreds of reviews. The seed is idempotent — existing data is never overwritten. Delete `data/grandhaven.db` to reseed from scratch.

---

## 🔍 Search System

The homepage search bar validates:

- Destination must not be empty
- Check-in must not be empty / cannot be in the past
- Check-out must not be empty and must be **after** check-in
- Guest count must be between 1 and 12

On a valid submit, the criteria are preserved in the URL and the app navigates to `/hotels?destination=…&checkIn=…&checkOut=…&guests=…`, where the results page queries the database. A room is shown when it matches the destination, accommodates the guest count, and has **no overlapping confirmed booking** for the requested dates (real availability, not static).

## 🎛 Filters & Sorting

All filters update the URL and re-query the database — results genuinely change:

- **Price range** — min/max per-night price (debounced as you type)
- **Hotel rating** — 3+, 4+, or 5+ guest rating
- **Amenities** — rooms must include every selected amenity (amenities live on both rooms and hotels, e.g. Pool/Gym/Breakfast are hotel facilities)

Sorting reorders the result set: **Recommended** (rating-weighted score), **Price: Low to High**, **Price: High to Low**, **Best Rated** (guest rating, then review count).

## 🛏 Booking System

1. Choose a room (any card's **Reserve** button or the room details page).
2. The booking page preloads your search dates/guests and shows a live summary — nights, price per night, base price, 12% tax, 5% service fee, and total are **calculated dynamically**, never hardcoded.
3. Fill in first/last name, email, phone, guests, dates, and optional special requests. All fields are validated with clear messages.
4. **Confirm Booking** sends the reservation to the server, which re-validates everything, recomputes the price server-side, checks availability again, and inserts the booking into the database.
5. Only after a successful insert does the **"Booking Confirmed! 🎉"** popup appear with the unique confirmation number and full reservation details.
6. If the save fails (room already booked, too many guests, validation error, database error), a **"Booking Failed"** dialog explains the problem and offers **Try Again** — a false success is never shown.
7. Every confirmed booking is permanently visible under **My Bookings** (`/bookings`).

---

## 🧪 Testing

Manual end-to-end flow (all verified):

```
Home → search Dubai (Aug 17 – Aug 19, 2 guests)
→ results → filter price ≤ $200 → filter Balcony → sort Price: Low to High
→ open Deluxe King Room → browse gallery → Reserve
→ fill guest details → review nights/total → Confirm Booking
→ "Booking Confirmed! 🎉" popup with HIL-2026-XXXXXX
→ My Bookings shows the saved booking
```

Run a production build as a smoke test:

```bash
npm run build
```

---

## ☁️ Deploying to Cloudflare Pages

Cloudflare Pages serves the built frontend on a public `*.pages.dev` URL, and the app talks to Supabase directly — **no server to run**. Deploy a couple of ways:

### Option A — Dashboard (easiest)

1. Push this repository to GitHub/GitLab.
2. Cloudflare Dashboard → **Workers & Pages → Create → Pages → Connect to Git**.
3. Pick the repo. Build settings:
   - **Build command:** `npm run build`
   - **Output directory:** `dist`
4. Under **Settings → Environment variables**, add:
   - `VITE_SUPABASE_URL` = your Supabase project URL
   - `VITE_SUPABASE_ANON_KEY` = your anon / publishable key
5. Deploy. You get a public URL like `https://grandhaven-hotels.pages.dev` — share that link anywhere (Facebook, etc.).

### Option B — CLI

```bash
npm run build
npx wrangler pages deploy dist
# then set the two VITE_SUPABASE_* vars in the Pages project settings
```

`public/_redirects` (`/* /index.html 200`) makes deep links like `/room/12` work on refresh.

### Alternative — Node host (local mode)

The app also deploys to any Node host (Render, Railway, Fly.io, a VPS):

1. `npm install`
2. `npm run build`
3. `npm start` (Express serves the built SPA + API on `API_PORT`, default 4000)

Make sure the `data/` directory is writable by the process (it holds the SQLite file), or use Supabase mode instead.

---

## 📄 License

[MIT](LICENSE)

## 🙏 Credits

Original implementation inspired by premium hotel booking experiences. Photography is from the free-to-use Unsplash CDN; no proprietary assets or code are used.
