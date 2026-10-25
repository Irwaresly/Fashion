# ATELIER — Operating Manual & Project Rules

This document defines the constraints, patterns, design tokens, and protocols that must be followed on this project. Read this file first, every session.

---

## 1. Project Overview & Architecture
* **Product:** ATELIER — Fashion AI Discovery & Digital Styling Platform.
* **Stack:** Next.js 14 (App Router), TypeScript, Tailwind CSS, Supabase (PostgreSQL + Auth + Storage), `@mediapipe/tasks-vision`, Lucide React, Zustand, `@tanstack/react-query`.
* **Methodology:** Mobile-first, strict component modularity, zero-hallucinated packages, canonical documentation as source of truth.

---

## 2. Canonical Docs Reference (Source of Truth)
Before modifying or creating any feature, read and conform to:
1. `PRD.md` — Product requirements, in-scope features, non-goals.
2. `APP_FLOW.md` — Routes, transitions, error/success states.
3. `TECH_STACK.md` — Approved libraries and locked versions. Never add unapproved packages.
4. `FRONTEND_GUIDELINES.md` — Color hex codes, typography, bento grids, spacing scale.
5. `BACKEND_STRUCTURE.md` — Database tables, foreign keys, RLS policies, storage buckets.
6. `IMPLEMENTATION_PLAN.md` — Step-by-step build sequence. Build only the current active step.

---

## 3. Persistent Memory Protocols
* **At Start of Every Session:**
  1. Read `progress.txt` to see what is completed, what is in progress, and what is next.
  2. Read `lessons.md` to avoid repeating past mistakes.
* **At End of Every Feature/Step:**
  1. Update `progress.txt` with exact details of what was built, what works, and what's next.
  2. If any mistake or friction occurred, record the prevention rule in `lessons.md`.
  3. Commit working code to git with a descriptive message.

---

## 4. Code & Architecture Rules
* **Directory Structure:**
  ```
  src/
  ├── app/                  # Next.js App Router pages and API routes
  │   ├── (auth)/           # Login, Signup routes
  │   ├── (main)/           # Feed, Wardrobe, Try-On, Boutiques, Avatar
  │   └── api/              # Affiliate redirect, VTON, segmentation proxies
  ├── components/           # Reusable UI pieces
  │   ├── ui/               # Core atomic primitives (Button, Card, Dialog, Sheet)
  │   ├── layout/           # Navbar, MobileDock, Sidebar, Shell
  │   ├── feed/             # BentoGrid, PostCard, TagPin
  │   ├── wardrobe/         # GarmentCard, ClosetGrid, IngestionModal
  │   ├── try-on/           # FittingCanvas, MannequinSelector, WardrobeDrawer
  │   └── boutique/         # BoutiqueCard, ProductGrid
  ├── lib/                  # Utilities, Supabase clients, MediaPipe helpers
  ├── store/                # Zustand client stores (wardrobe, fitting room)
  └── types/                # Strict TypeScript contracts matching DB schema
  ```
* **Strict Conventions:**
  * All components must be in `src/components/` and use TypeScript (`.tsx`).
  * Never use inline CSS styles. Use Tailwind CSS classes matching tokens in `FRONTEND_GUIDELINES.md`.
  * All icons must be imported from `lucide-react`.
  * Every server request must handle loading and error states gracefully.
  * Secrets and API keys belong strictly in `.env.local` and must never be exposed to the browser.
  * Every clickable external link must open safely (`target="_blank" rel="noopener noreferrer"`).
  * Affiliate clicks must always be routed through `/api/affiliate/[tagId]`.
