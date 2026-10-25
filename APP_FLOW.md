# Application Flow (APP_FLOW.md) — ATELIER

**Document Status:** LOCKED / SOURCE OF TRUTH  
**Platform Target:** Next.js 14 App Router (Mobile-First Web / PWA)

---

## 1. Route Inventory & Navigation Map

```
/ (Root)
│
├── / (Discovery Feed) ──────────────── [Masonry/Bento Creator Lookbook]
│   └── /post/[id] ──────────────────── [Look Details, Interactive Tags, Comments]
│
├── /try-on ─────────────────────────── [Interactive Virtual Fitting Room]
│   └── ?garmentId=xxx ──────────────── [Pre-loads target garment onto active avatar]
│
├── /wardrobe ───────────────────────── [Digital Closet Grid & Filter Engine]
│   └── /wardrobe/upload ────────────── [Garment Snap, Auto-Cutout & Metadata Form]
│
├── /avatar ─────────────────────────── [Avatar Configuration & MediaPipe Setup]
│
├── /boutiques ──────────────────────── [Boutique Directory]
│   └── /boutique/[slug] ────────────── [Boutique Storefront & Inventory Catalog]
│
├── /creator/[handle] ───────────────── [Creator Profile, Lookbook & Commission Feed]
│
├── /auth
│   ├── /auth/login ─────────────────── [Email, Google, Apple Login]
│   └── /auth/signup ────────────────── [Onboarding Registration]
│
└── /api
    ├── /api/affiliate/[tagId] ──────── [Click Attribution Logger & 302 Redirect]
    ├── /api/vton/process ───────────── [Serverless Try-On Trigger / Proxy]
    └── /api/segmentation/remove-bg ─── [RMBG-1.4 Background Removal Proxy]
```

---

## 2. Detailed User Navigation Flows

### Flow 1: Public Discovery & Guest Browsing
* **Trigger:** User lands on `https://domain.com/`
* **Step 1:** Next.js Server Component streams the Discovery Feed (Bento/Masonry grid).
* **Step 2:** User can browse public creator posts, inspect high-res photos, and view garment tags without logging in.
* **Step 3 (Decision Point):**
  * If user clicks **"Try on Me"**:
    * *Authenticated:* Immediately redirects to `/try-on?garmentId={id}` with the garment loaded.
    * *Unauthenticated:* Opens the Auth Modal / redirects to `/auth/signup?redirect=/try-on?garmentId={id}`.
  * If user clicks an external **"Buy Item"** tag:
    * Browser opens `/api/affiliate/[tagId]` in a new tab $\rightarrow$ logs `affiliate_clicks` row $\rightarrow$ 302 redirects to merchant store.

### Flow 2: Wardrobe Digitization (Ingestion Loop)
* **Trigger:** User clicks **"+"** or **"Add Garment"** in navigation (`/wardrobe/upload`).
* **Step 1 (Source Selection):** User snaps a photo via mobile camera or uploads from device gallery.
* **Step 2 (Segmentation):**
  * Image is posted to `/api/segmentation/remove-bg`.
  * Background is stripped via RMBG-1.4 model; clean transparent PNG is generated.
  * Preview of the clean cutout is displayed instantly.
* **Step 3 (Metadata Extraction & Confirmation):**
  * Auto-detected category (Top, Bottom, Outerwear, Footwear, Accessory) and extracted HEX palette.
  * User can edit category, title, brand, and seasonal tags.
* **Step 4 (Save):**
  * Raw image stored in Supabase `wardrobe-raw` bucket.
  * Clean cutout stored in Supabase `wardrobe-clean` bucket.
  * Metadata record inserted into `wardrobe_items` table.
* **Success State:** Toast notification `"Garment added to your closet!"` $\rightarrow$ redirects to `/wardrobe`.
* **Error State:** If cutout fails, user can proceed with original crop or retry with clear lighting.

### Flow 3: Avatar Setup & MediaPipe Keypoints
* **Trigger:** User navigates to `/avatar`.
* **Step 1:** User chooses between:
  * **Option A (Instant Mannequin):** Select from 6 diverse standardized 2D fashion silhouettes.
  * **Option B (Custom Photo):** Upload a full-length personal photo.
* **Step 2 (Local Processing):**
  * Client-side `@mediapipe/tasks-vision` Pose Landmarker executes on the uploaded image.
  * 33 3D body keypoints (shoulders, hips, torso, knees, ankles) are calculated client-side with 0 server latency.
* **Step 3:** Keypoints and avatar image URL are saved to `profiles` table.
* **Success State:** Avatar becomes active in `/try-on` fitting room.

### Flow 4: Virtual Try-On (VTON) Execution
* **Trigger:** User is in `/try-on` room or clicked "Try on Me" from feed.
* **Step 1:** Canvas loads active user avatar as base layer.
* **Step 2:** User selects a garment from their digitized wardrobe OR from the affiliate catalog.
* **Step 3:** User taps **"Generate Try-On"**:
  * If Fal.ai/Replicate API keys are present: Calls IDM-VTON serverless endpoint; returns photorealistic diffused render in 3–6s.
  * If API keys absent / fallback active: MediaPipe pose coordinates align and warp the clean PNG garment layer over the avatar using HTML5 Canvas Compositing.
* **Step 4:** User can save the resulting outfit to `outfits` or share as a new `feed_posts` lookbook entry.

### Flow 5: Local Boutique Inventory Management & Marketplace
* **Trigger:** Boutique owner navigates to `/boutique/[slug]`.
* **Step 1:** Boutique views their digitized storefront.
* **Step 2:** Boutique clicks **"Add Product"**:
  * Uploads photo, adds garment name, sizes available, stock status, price, and payment link.
* **Step 3:** Product is published to `boutique_products` table and surfaces in platform search and feed tags.
* **Step 4:** When a shopper tries on and buys from the boutique, transaction record logs fee split.
