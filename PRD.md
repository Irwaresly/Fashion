# Product Requirements Document (PRD) — ATELIER

**Product Name:** ATELIER (Fashion AI Discovery & Digital Styling Platform)  
**Version:** 1.0 (MVP)  
**Document Status:** LOCKED / SOURCE OF TRUTH  

---

## 1. Executive Summary & Problem Statement

### The Problem
* **Outfit Decision Fatigue:** Daily clothing selection is mentally taxing; users underutilize 70%+ of their existing wardrobe because they cannot visualize pairings.
* **Return Rates & Try-On Friction:** Online fashion shopping suffers from 30–40% return rates due to uncertainty around fit, styling, and how new purchases match existing clothes.
* **Monetization Bottleneck for Micro-Influencers:** Content creators style outfits but struggle to monetize without building complex e-commerce stores.
* **Digital Gap for Local Boutiques:** Independent local boutiques lack the resources to build interactive virtual fitting rooms and reach digital-native shoppers.

### The Solution
**ATELIER** is a social fashion discovery and digital styling platform. Users photograph their wardrobe to generate AI-curated outfits on a personalized avatar, while browsing a shoppable community feed where creators earn affiliate commissions and local boutiques monetize their inventories when followers try on and buy tagged pieces.

---

## 2. Target Personas

| Persona | Description | Core Need |
| :--- | :--- | :--- |
| **The Everyday Stylist (Core Consumer)** | Ages 18–35, fashion-conscious, shopping online and on social media. | Digitize closet, visualize daily outfits on their avatar, and preview clothes before purchasing. |
| **The Fashion Creator (Influencer)** | Micro-influencers, stylists, and fashion curators. | Showcase curated looks with interactive tags and earn automated commissions on click-throughs. |
| **The Independent Boutique** | Local fashion retailers and independent designers. | Digitize store inventory into the styling engine and sell to local/digital shoppers with zero store infrastructure. |

---

## 3. Core User Journeys

### User Journey 1: Wardrobe Ingestion & Virtual Try-On (The Core Loop)
1. User snaps a photo of a clothing item or uploads from gallery.
2. Background is automatically stripped (RMBG-1.4), garment is categorized and tagged with color palette.
3. Garment is saved into user's digital wardrobe.
4. User selects items (or taps "Generate Look") to preview the outfit on their personalized avatar powered by IDM-VTON.

### User Journey 2: Social Discovery & Creator Affiliate Conversion
1. User scrolls the discovery feed (Bento/Masonry layout) showcasing creator looks.
2. User taps an interactive garment tag on a creator's post.
3. User taps **"Try This Look on Me"**; the garment is rendered on their avatar in seconds.
4. User clicks **"Buy Item"**; request is logged via affiliate tracking (`/api/affiliate/[tagId]`) and redirects to merchant checkout.
5. Creator earns affiliate commission on sale.

### User Journey 3: Local Boutique Storefront & Inventory Ingestion
1. Boutique owner creates a boutique profile.
2. Boutique uploads collection pieces with price, sizes, and direct checkout link.
3. Pieces become immediately available for any user on the platform to try on their avatar.
4. Platform collects a transaction fee on boutique sales made through profiles.

---

## 4. Scope: Must-Haves (MVP) vs. Non-Goals

### Strictly In-Scope (MVP)
* **Wardrobe Digitization Engine:** Single/batch photo upload, edge background removal (RMBG-1.4), category/color extraction, digital closet management.
* **Avatar & Pose Estimation:** Standardized 2D avatar mannequins + custom photo upload; MediaPipe pose keypoint detection running locally in-browser.
* **2D Virtual Try-On (VTON):** High-fidelity virtual try-on via IDM-VTON (Fal.ai / Replicate serverless endpoints) with local canvas compositor fallback.
* **Social Discovery Lookbook:** Masonry/Bento layout feed with high-res editorial posts, interactive garment tags, like/save counters, and share actions.
* **Affiliate & Commission Engine:** Tracked redirect endpoint (`/api/affiliate/[id]`), click attribution for creators, support for global affiliate links (Skimlinks, Amazon, direct).
* **Boutique Mini-Storefront:** Boutique profile page, inventory catalog, and direct purchase referral.
* **Authentication & RLS:** Supabase Auth (Email, Google, Apple) with strict Row-Level Security on personal wardrobes.

### Explicitly Out-of-Scope (Non-Goals for V1)
* **3D Volumetric Mesh Physics (Cloth Simulation):** No real-time cloth-on-avatar draping (CLO3D/Marvelous Designer).
* **Native In-App Multi-Vendor Shipping/Logistics:** No merchant inventory warehousing or split shipping fulfillment in MVP.
* **Real-time Video Try-On:** No video diffusion rendering (too high latency and cost-prohibitive).
* **Direct Credit Card Processing for Third-Party Retailers:** All external purchases are routed directly to merchant checkouts.

---

## 5. Success Metrics & Performance Criteria

| Metric | Target |
| :--- | :--- |
| **Ingestion to Wardrobe Time** | $< 3$ seconds per garment (including background removal). |
| **Virtual Try-On Render Latency** | $< 6$ seconds on GPU endpoint; instant preview on canvas fallback. |
| **Mobile Responsiveness** | 100% functional on mobile browsers (viewport 360px+). |
| **Affiliate Tracking Accuracy** | 100% of outbound clicks recorded in database with creator attribution. |
