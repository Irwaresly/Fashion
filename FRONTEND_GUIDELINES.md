# Frontend Design Guidelines (FRONTEND_GUIDELINES.md) — ATELIER

**Document Status:** LOCKED / SOURCE OF TRUTH  
**Aesthetic Language:** Editorial Minimalist & High-Utility Bento Grid  
**Inspiration:** SSENSE, Acne Studios, Pinterest Masonry, Cider Mobile

---

## 1. Visual Philosophy & Design Principles
1. **The Canvas Steps Back:** The UI is stark, quiet, and razor-sharp. Clothing textures, silhouettes, and avatar renders are the heroes.
2. **Tactile Micro-Interactions:** Subtle haptic-style transitions, glassmorphic tag pins, smooth bottom-sheet modals on mobile.
3. **No Clutter:** Maximum whitespace, strict 4px grid rhythm, high contrast, zero unnecessary decorative borders.

---

## 2. Typography

| Role | Font Family | Fallback | Tracking | Usage |
| :--- | :--- | :--- | :--- | :--- |
| **Display / Headings** | `Cabinet Grotesk` or `Inter` | `system-ui, -apple-system, sans-serif` | `-0.03em` (tight) | Hero titles, boutique brand names, lookbook headers. |
| **Body / UI** | `Inter` | `sans-serif` | `-0.01em` | Buttons, descriptions, navigation, comments. |
| **Data / Accents** | `JetBrains Mono` or `Geist Mono` | `monospace` | `0em` | Prices, SKU codes, color HEX codes, body measurement metrics. |

---

## 3. Color Tokens (Tailwind Palette)

```javascript
// tailwind.config.js token extensions
module.exports = {
  theme: {
    extend: {
      colors: {
        atelier: {
          bg: '#09090B',          // Pitch obsidian background (dark mode primary)
          surface: '#121215',     // Elevated card / bento tile background
          surfaceElevated: '#1A1A1F', // Dropdowns, modals, floating inspectors
          border: '#27272A',      // Subtle hairline borders
          borderLight: '#3F3F46', // Hover border highlights
          textPrimary: '#FAFAFA', // High-contrast editorial white
          textSecondary: '#A1A1AA', // Muted metadata, timestamps, categories
          accent: '#E4E4E7',      // Primary buttons & active indicators
          gold: '#D4AF37',        // Boutique verified badge & luxury accents
          tagBg: 'rgba(0, 0, 0, 0.72)', // Glassmorphic garment tag pin background
        }
      }
    }
  }
}
```

---

## 4. Spacing Scale & Layout Grid

* **Base Unit:** 4px
* **Scale:**
  * `4px` (`p-1`) — Micro gaps, badge padding.
  * `8px` (`p-2`) — Icon buttons, tag margins.
  * `12px` (`p-3`) — Compact card padding, input fields.
  * `16px` (`p-4`) — Standard card interior, mobile screen padding.
  * `24px` (`p-6`) — Section spacing, modal margins.
  * `32px` (`p-8`) — Desktop container padding.
  * `48px` (`p-12`) — Editorial hero margins.

### Responsive Breakpoints
* **Mobile (default):** `0px - 639px` (Single-column masonry, bottom navigation bar, swipeable sheets).
* **Tablet (`md:`):** `640px - 1023px` (2-column bento grid, sidebar icons).
* **Desktop (`lg:`):** `1024px+` (3-column lookbook masonry, fixed sidebar navigation, split-screen try-on room).

---

## 5. Signature Components & Patterns

### 1. The Interactive Garment Tag Pin
* Floating coordinate pill over lookbook imagery.
* Visual style: `backdrop-blur-md bg-black/75 text-white border border-white/20 text-xs px-2.5 py-1 rounded-full shadow-lg`.
* Includes item title, price, and instant **"Try on Me"** action button.

### 2. The Bento Lookbook Card
* Variable aspect ratios (`aspect-[3/4]` or `aspect-[4/5]`) to prevent repetitive grids.
* Subtle zoom transition on hover (`group-hover:scale-105 transition-transform duration-500 ease-out`).
* Bottom gradient overlay for legible creator credits and metrics.

### 3. The Virtual Fitting Room Canvas
* Split interface on desktop (50% avatar canvas / 50% wardrobe selector).
* Bottom sheet drawer on mobile.
* Floating control pill at bottom: `[Undo] [Change Avatar] [Generate Diffused Try-On] [Save Outfit]`.
