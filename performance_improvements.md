# Lighthouse Performance Analysis & Improvement Plan

**Date:** 2025-12-22
**Status:** In Progress 🟡

## 📊 Findings from Lighthouse Report
The initial Lighthouse report (run on `localhost:5173`) indicated areas for improvement:
- **First Contentful Paint (FCP):** 2.8s (Poor)
- **Largest Contentful Paint (LCP):** 5.3s (Poor)
- **Speed Index:** 3.2s (Poor)

### Contextual Note
The test was run against the **Vite Development Server** (`localhost:5173`). Development builds are unminified, use unoptimized ESM modules, and contain hot-module-replacement (HMR) overhead. This significantly skews performance metrics negatively compared to a production build.

## 🔍 Codebase Analysis
- **Routing:** Route-level code splitting (lazy loading) is correctly implemented in `router/index.js`.
- **Assets:** Google Fonts `Orbitron`, `Inter`, and `Atkinson Hyperlegible` are loaded from external CDN in `index.html`. This is a render-blocking request.
- **Images:** The Hero image `/favicon.webp` is small, but loaded dynamically within the component lifecycle.
- **Styling:** Tailwind CSS is used with inline configuration.

## 🚀 Recommended Improvements

### 1. ⚠️ Verify on Production Build (Critical)
Run the audit against a production preview to get accurate metrics.
```bash
npm run build
npm run preview
# Open the preview URL (usually localhost:4173) in Lighthouse
```

### 2. Self-Host Fonts
Eliminate the latency of connecting to `fonts.googleapis.com` and `fonts.gstatic.com` by hosting font files locally.
- **Action:** Download `.woff2` files for Orbitron, Inter, and Atkinson Hyperlegible.
- **Action:** Place them in `frontend/src/assets/fonts/`.
- **Action:** Define `@font-face` rules in `main.css` and remove `<link>` tags from `index.html`.

### 3. Preload Critical Assets
Ensure the browser prioritizes key resources.
- **Action:** Add `<link rel="preload" href="/favicon.webp" as="image">` to `index.html` if it remains the LCP element.
- **Action:** Preload the critical font (likely `Orbitron` for headers) once self-hosted.

### 4. Optimize LCP (Largest Contentful Paint)
The spinner in `index.html` is the first paint, but the LCP event likely fires when `Home.vue` renders.
- **Action:** Ensure the loading spinner transitions smoothly.
- **Action:** Consider fetching critical data or assets earlier if possible (not applicable for static Home page).

### 5. Config Optimization
Ensure `vite.config.js` splits chunks effectively.
- **Action:** Verify `splitVendorChunkPlugin` or manual `rollupOptions` output configuration if the main bundle is too large.

## ✅ Next Steps
1.  **[ ] User to confirm** if they want to proceed with **Self-Hosting Fonts** immediately.
2.  **[ ] User to run Lighthouse** on a production build to confirm baseline.
