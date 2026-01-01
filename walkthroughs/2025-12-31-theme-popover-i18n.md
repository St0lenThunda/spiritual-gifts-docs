# Theme Popover i18n & Visibility

**Date**: December 31, 2025
**Status**: Completed ✅

## Summary
Improved the visibility and internationalization of the theme preset popovers in the branding settings. Standardized the popover styling using semantic variables and implemented translations for "Primary" and "Accent" color labels across all supported languages.

## Issues Addressed / Features Added
- Fixed "white on white" text visibility issue in theme presets popover.
- Standardized popover background and text colors using semantic variables (e.g., `bg-bg-surface`, `text-text-main`).
- Implemented i18n translations for "Primary" and "Accent" color labels.
- Added localized strings for English, Spanish, French, and Russian.

## Implementation Details
- Modified `BrandingSettings.vue` to:
    - Replace hardcoded colors with semantic variables.
    - Use `$t('org.branding.primary_label')` and `$t('org.branding.accent_label')`.
- Updated locale files (`en.json`, `es.json`, `fr.json`, `ru.json`) with the new labels.
- Added a unit test to verify the fix.

## Files Created/Modified
- `frontend/src/components/organization/BrandingSettings.vue` - Updated popover styling and i18n.
- `frontend/src/locales/en.json` - Added labels.
- `frontend/src/locales/es.json` - Added labels.
- `frontend/src/locales/fr.json` - Added labels.
- `frontend/src/locales/ru.json` - Added labels.
- `frontend/src/components/organization/__tests__/BrandingSettings.spec.js` - New unit test.

## Verification
- Ran Vitest unit test for `BrandingSettings.vue`: **Passed**.
- Verified translation keys consistency across all locale files.
