# Lotus House — Community Lotus Tub Management Platform

*A shared, browser-based registry for lotus growers to track tubs, blooms, and grower notes — built from scratch with vanilla JavaScript and a hand-written Python backend.*

![Lotus in bloom](Flowers/IMG-20260908-WA0020.jpg)

---

## Overview

Lotus House is a full front-to-back prototype for a community lotus-growing collective: a place where members can register a numbered "tub" (a container-grown lotus plant), attach photo/video proof of its condition, and browse a shared ledger of every tub in the garden. It started as a single-page site and grew into a small multi-view application with its own lightweight backend for handling media uploads.

The project was built without any frontend framework or backend web framework — just HTML, CSS, JavaScript, and Python's standard library — as a way to get hands-on with routing, state, file handling, and form/media upload logic from first principles rather than through an abstraction.

## The Problem It Solves

Lotus growers who share a common garden or pond space (a real pattern in home-gardening communities) have no easy shared way to know: which tub is which, what's planted in it, who supplied it, and how it's doing. Lotus House gives that group a simple public ledger — one page to register a new tub, one page to search/browse all of them, and a gallery to enjoy the results.

## What I Built

The app has five main views, all handled client-side through hash-based routing (`#home`, `#register`, `#database`, `#garden`, `#flowers`):

- **Home** — a marketing-style landing page with a rotating image hero, an intro section, and calls to action into the register.
- **Tub Register** — the core workflow: a form to add a new tub record (number, plant name, classification, date, seller, remarks) plus optional photo/video proof, alongside a live, searchable list of every record added so far.
- **Tub Database** — a spreadsheet-style table view of all 500 fixed tub slots (auto-generated as empty rows until filled), with inline favoriting and search by number, flower, or seller.
- **Garden View** — a dashboard-style summary (registered tub count, a "days to next bloom" metric, a recent-activity feed) giving an at-a-glance read of the garden's state.
- **Flower Archive** — a lazy-loaded image gallery of 33 real garden photos with a lightbox viewer.

## Architecture & Tech Stack

| Layer | Choice | Why |
|---|---|---|
| Frontend | Vanilla JS, HTML5, CSS3 | No framework overhead; full control over routing and rendering |
| Routing | Hash-based SPA routing with `history.pushState` | Back/forward navigation without a bundler or router library |
| Record storage | Browser `localStorage` | Zero-setup persistence for a single-user prototype |
| Media storage | Python `http.server` backend | Actual files (photos/videos) need real disk storage, not `localStorage` |
| Backend runtime | `ThreadingHTTPServer` (Python standard library only) | Handles concurrent uploads without any external dependency |

## Technical Highlights

- **Hand-written multipart/form-data parser.** File uploads (`/api/upload`) are parsed directly from the raw request bytes — splitting on the multipart boundary and extracting the filename, content type, and payload — without relying on an external multipart-parsing library.
- **Media lifecycle management.** A second endpoint (`/api/media-action`) moves an uploaded file between `storage/`, `storage/favorites/`, and `storage/deleted/` when a user favorites, unfavorites, or removes a tub record — with automatic collision-safe renaming (`name_1.ext`, `name_2.ext`, …) if a file of the same name already exists in the destination.
- **Tub numbering system.** Tub numbers are validated to a 1–9999 range, zero-padded to four digits for display, and checked against existing records to block duplicate registrations — all enforced through the HTML5 Constraint Validation API (`setCustomValidity`) for native browser error messages.
- **Live search across two independent views.** Both the register list and the full 500-row database table filter in real time against tub number, flower name, classification, and seller, without a page reload.
- **Responsive lightbox gallery** for the flower archive, with keyboard (`Escape`) and click-outside dismissal.
- **GDPR/CCPA-style cookie consent banner** with granular category toggles (necessary/analytics/marketing), persisted to both `localStorage` and a cookie, synced live across browser tabs via the `storage` event, and exposed as a small public API (`window.lotusCookieConsent`) so other scripts (e.g. an analytics snippet) can react to consent changes.

## What's Prototype-Only (By Design)

Being transparent about scope is part of the case study:

- The **Garden View** dashboard's stats (bloom countdowns, activity feed) are illustrative placeholders, not computed from live data — a stand-in for what a real backend-driven dashboard would show.
- The **Join/signup** form is UI-only; it doesn't create real accounts.
- Tub records live in the browser's `localStorage`, so the register is single-user/single-browser in this prototype. A shared, multi-user version would need a real database and authentication layer behind the existing upload/media API, which was designed with that extension in mind.

## Project Stats

- ~2,900 lines of hand-written HTML, CSS, JS, and Python — no frontend framework, no backend web framework
- 5 interactive views + a working file-upload backend
- 33 original garden photographs integrated into a working gallery
- 500-slot numbered registry system with live search and favoriting

## Running It Locally

```bash
python server.py
# then open http://localhost:4173
```

The Python server is required for photo/video uploads to persist — opening `index.html` directly will still let you browse the UI, but uploads will fail without it running.

---

*Built by Shreyansh Singh — [github.com/Shreyansh-DevHub](https://github.com/Shreyansh-DevHub)*
