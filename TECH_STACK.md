# Technical Stack (TECH_STACK.md) — ATELIER

**Document Status:** LOCKED / SOURCE OF TRUTH  
**Rule:** No unapproved packages or version drift allowed.

---

## 1. Core Framework & Runtime

| Component | Technology | Exact Version | Rationale |
| :--- | :--- | :--- | :--- |
| **Framework** | Next.js (App Router) | `14.2.15` | Hybrid SSR/SSG, Server Actions, high-performance image optimization. |
| **Language** | TypeScript | `^5.6.3` | Strict type safety for data contracts and API responses. |
| **UI Library** | React / React DOM | `^18.3.1` | Concurrent features, stable ecosystem for media/canvas hooks. |
| **Node.js Target** | Node.js | `>=20.x` LTS | Modern ES modules, fetch API, and edge compatibility. |

---

## 2. Styling, Icons & UI Primitives

| Component | Package / Tool | Exact Version | Purpose |
| :--- | :--- | :--- | :--- |
| **CSS Engine** | Tailwind CSS | `^3.4.14` | Utility-first styling with locked design tokens. |
| **PostCSS** | PostCSS / Autoprefixer | `^8.4.47` / `^10.4.20` | CSS transformations and cross-browser vendor prefixing. |
| **Animations** | `tailwindcss-animate` | `^1.0.7` | High-polish micro-interactions and transitions. |
| **UI Primitives** | Radix UI (`@radix-ui/*`) | `^1.x` | Accessible, unstyled primitives for dialogs, dropdowns, tabs, sheets. |
| **Utility Helpers** | `clsx` + `tailwind-merge` | `^2.1.1` / `^2.5.4` | Clean class consolidation (`cn` helper). |
| **Iconography** | `lucide-react` | `^0.453.0` | Minimal, high-fashion stroke icon suite. |

---

## 3. State Management & Data Fetching

| Role | Package | Exact Version | Description |
| :--- | :--- | :--- | :--- |
| **Client State** | `zustand` | `^5.0.0` | Snappy, lightweight state for wardrobe filters, fitting room layers, active avatar. |
| **Server State & Cache** | `@tanstack/react-query` | `^5.59.0` | Asynchronous feed caching, infinite scroll, optimistic updates. |

---

## 4. Backend, Database, Auth & Storage

| Role | Provider / Package | Exact Version | Description |
| :--- | :--- | :--- | :--- |
| **Database** | Supabase (PostgreSQL 15+) | Managed | Relational schema with pgvector for visual feature embeddings. |
| **Authentication** | Supabase Auth | `@supabase/ssr` `^0.5.1` & `@supabase/supabase-js` `^2.45.6` | Session cookies, Google OAuth, Apple Sign-In, Email/Password. |
| **Object Storage** | Supabase Storage | Built-in | S3-compatible buckets for `wardrobe-raw`, `wardrobe-clean`, `avatars`, `feed-posts`. |

---

## 5. AI, Computer Vision & Try-On Pipeline

| Subsystem | Technology | Deployment | Description |
| :--- | :--- | :--- | :--- |
| **Pose Keypoints** | `@mediapipe/tasks-vision` | `^0.10.17` (Client WASM) | 33 3D body landmark detection running on-device with zero server costs. |
| **Background Removal** | RMBG-1.4 (BRIA AI) | Serverless API (Fal.ai / Replicate) | Sub-second high-precision garment cutout generation. |
| **Virtual Try-On** | IDM-VTON (Diffusion) | Serverless GPU (Fal.ai / Replicate) | 2D latent image-to-image virtual try-on preserving garment folds and textures. |
| **Fallback Compositor** | HTML5 Canvas API | Native Browser | High-speed client-side affine warp and alpha-blending fallback when offline/no API keys. |

---

## 6. Development, Linting & Type Checking

```json
{
  "devDependencies": {
    "typescript": "^5.6.3",
    "@types/node": "^20.17.0",
    "@types/react": "^18.3.11",
    "@types/react-dom": "^18.3.1",
    "eslint": "^8.57.1",
    "eslint-config-next": "14.2.15",
    "postcss": "^8.4.47",
    "tailwindcss": "^3.4.14"
  }
}
```
