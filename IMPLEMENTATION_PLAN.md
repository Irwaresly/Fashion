# Implementation Plan (IMPLEMENTATION_PLAN.md) — ATELIER

**Document Status:** LOCKED / SOURCE OF TRUTH  
**Rule:** Work step-by-step. Do not jump ahead. Update `progress.txt` after every completed step.

---

## Phase 1: Project Scaffolding & Environment Setup
- [ ] **Step 1.1:** Initialize Next.js 14 App Router project with TypeScript, ESLint, and Tailwind CSS.
- [ ] **Step 1.2:** Install locked core dependencies (`@supabase/supabase-js`, `@supabase/ssr`, `lucide-react`, `zustand`, `@tanstack/react-query`, `clsx`, `tailwind-merge`, `tailwindcss-animate`).
- [ ] **Step 1.3:** Configure `tailwind.config.js` with Atelier color tokens, font stacks, and responsive layout scales per `FRONTEND_GUIDELINES.md`.
- [ ] **Step 1.4:** Create `.env.example` with Supabase, Fal.ai/Replicate, and app keys; configure `.gitignore`.
- [ ] **Step 1.5:** Configure Supabase browser and server client utilities in `src/lib/supabase/`.

## Phase 2: Design System & Core Shell Primitives
- [ ] **Step 2.1:** Create standard UI utility (`cn`) and base component primitives: `Button`, `Card`, `Badge`, `Input`, `Dialog`, `Sheet`.
- [ ] **Step 2.2:** Build global responsive navigation shell: desktop sidebar + mobile bottom dock (`/`, `/wardrobe`, `/try-on`, `/boutiques`, `/avatar`).
- [ ] **Step 2.3:** Implement empty states, loading skeletons, and toast notification provider.

## Phase 3: Authentication & User Profiles
- [ ] **Step 3.1:** Implement `/auth/login` and `/auth/signup` with email/password and social provider triggers.
- [ ] **Step 3.2:** Build profile settings page (`/settings/profile`) with body measurements and handle management.
- [ ] **Step 3.3:** Implement auth middleware to protect `/wardrobe` and `/try-on` personal state.

## Phase 4: Wardrobe Ingestion & Background Removal Pipeline
- [ ] **Step 4.1:** Build camera snap & drag-and-drop file uploader component (`/wardrobe/upload`).
- [ ] **Step 4.2:** Implement `/api/segmentation/remove-bg` proxy with RMBG-1.4 model and local canvas cutout fallback.
- [ ] **Step 4.3:** Build garment metadata editor (category selection, auto-extracted color palette, brand/price).
- [ ] **Step 4.4:** Build digital closet grid (`/wardrobe`) with filtering by category (top, bottom, outerwear, footwear, accessories) and season.

## Phase 5: Avatar Studio & MediaPipe Keypoint Detection
- [ ] **Step 5.1:** Integrate standardized 2D mannequin selector (6 diverse curated silhouettes).
- [ ] **Step 5.2:** Build custom full-body photo upload flow with in-browser `@mediapipe/tasks-vision` pose detection.
- [ ] **Step 5.3:** Extract and persist 33 3D body landmark vectors to `profiles.pose_landmarks`.
- [ ] **Step 5.4:** Build avatar preview card with active silhouette visualization.

## Phase 6: Virtual Try-On (VTON) Engine & Fitting Room
- [ ] **Step 6.1:** Build the split-screen Fitting Room interface (`/try-on`): Avatar viewer on left, Wardrobe selector on right.
- [ ] **Step 6.2:** Build client-side HTML5 canvas affine warp compositor (fallback instant try-on using MediaPipe keypoints).
- [ ] **Step 6.3:** Implement `/api/vton/process` serverless route integrating IDM-VTON diffusion on Fal.ai / Replicate.
- [ ] **Step 6.4:** Add outfit saving to `outfits` table and social lookbook publishing trigger.

## Phase 7: Social Discovery Feed & Interactive Garment Tagging
- [ ] **Step 7.1:** Build Bento/Masonry infinite-scroll lookbook feed (`/`).
- [ ] **Step 7.2:** Implement interactive floating garment tag pins over images (`PostTagPin`).
- [ ] **Step 7.3:** Wire the **"Try This Look on Me"** button to load the tagged garment directly into `/try-on?garmentId=xxx`.
- [ ] **Step 7.4:** Implement creator profile page (`/creator/[handle]`) displaying their styled lookbooks.

## Phase 8: Creator Affiliate Engine & Boutique Storefronts
- [ ] **Step 8.1:** Build click tracking & attribution route: `/api/affiliate/[tagId]` (logs to `affiliate_clicks` then 302 redirects).
- [ ] **Step 8.2:** Build creator analytics dashboard (`/creator/dashboard`) showing total impressions, try-on conversions, and estimated commissions.
- [ ] **Step 8.3:** Build boutique directory (`/boutiques`) and storefront page (`/boutique/[slug]`).
- [ ] **Step 8.4:** Build boutique inventory digitizer form with direct buy link and platform commission calculation.

## Phase 9: Verification, Optimization & Deployment
- [ ] **Step 9.1:** Perform cross-browser and mobile viewport testing (360px - 1440px).
- [ ] **Step 9.2:** Verify error boundaries, offline states, and empty states.
- [ ] **Step 9.3:** Run type checking (`tsc --noEmit`) and production build (`npm run build`).
- [ ] **Step 9.4:** Prepare deployment configuration for Vercel.
