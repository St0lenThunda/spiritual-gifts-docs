# Demo Banner, Default Dark Theme, and Results History

**Date**: December 27, 2025
**Status**: Completed ✅

## Summary
This implementation introduces a sitewide demo mode banner to prevent user confusion, establishes a modern dark baseline as the default theme, and enhances the results page to allow users to navigate their previous assessment history directly.

## Issues Addressed / Features Added
- **Demo Mode Transparency**: Added a sitewide amber banner for demo organizations.
- **Default Dark Theme**: Swapped the system baseline to "Midnight Sanctuary" (dark theme) for a premium out-of-the-box experience.
- **Improved Navigation Contrast**: Refined border and link visibility in dark mode.
- **Results Navigation**: Added a history selector to the Results page when no assessment is selected.
- **Dynamic UX**: Theme previews now happen in real-time in the branding settings.

## Implementation Details

### Theme Baseline Swap
The `:root` CSS variables in `main.css` were swapped with the dark theme values. An explicit `.theme-light` class was created to preserve the original light theme. Pinia stores (`organization.js` and `user.js`) were updated to default to `dark` when no preference is detected.

### Layout Management
Implemented dynamic padding on the root `App.vue` container and top offsets for the sticky header to accommodate stacked notification banners (Waking Server + Demo Mode) without overlapping content.

### Results Page Selection
The `Results.vue` page now checks for a `survey` query parameter. If missing, it fetches history via the `surveyStore` and renders a grid of clickable history cards. The `ResultsHeader` component was made dynamic to adjust instruction text based on the selection state.

## Files Created/Modified
- `frontend/src/assets/main.css` - Baseline theme swap and border contrast refinement.
- `frontend/src/App.vue` - Added demo banner and layout/padding logic.
- `frontend/src/stores/organization.js` - Added `isDemo` getter and dark default branding logic.
- `frontend/src/stores/user.js` - Updated default preferences to dark theme.
- `frontend/src/pages/Results.vue` - Added history selector and data fetching logic.
- `frontend/src/components/dashboard/ResultsHeader.vue` - Dynamic instructions based on selection.
- `frontend/src/components/organization/BrandingSettings.vue` - Real-time preview and demo mode restrictions.
- `frontend/src/locales/en.json` - New keys for demo and results history.
- `frontend/tests/e2e/default-theme-contrast.spec.js` - [NEW] E2E accessibility audit for dark baseline.

## Verification
- **Accessibility**: Ran `light-theme-contrast.spec.js` and the new `default-theme-contrast.spec.js` with 0 WCAG AA violations found.
- **Manual Verification**: Confirmed layout shifts correctly when banners are toggled, and history selection updates the charts as expected.
