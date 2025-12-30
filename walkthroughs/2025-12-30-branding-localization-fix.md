# Branding and Localization Fix

**Date**: December 30, 2024
**Status**: Completed ✅

## Summary
Resolved branding inconsistencies and localization warnings. The application title now correctly displays as "Called & Equipped" and localization strings no longer contain HTML, which was causing `vue-i18n` warnings and potential XSS risks.

## Issues Addressed / Features Added
- **Sanity Test Failures**: Fixed the document title in `src/router/index.js` which was causing E2E tests to fail.
- **[intlify] HTML Warning**: Refactored the brand name localization to use separate prefix and suffix keys instead of a single HTML string.
- **E2E Test Stability**: Seeded the development database with the required test users (`admin@grace.com`) to ensure production-readiness tests pass.

## Implementation Details

### Localization Refactor
To avoid using `v-html` and containing HTML tags in JSON localization files, I split `brand.name_html` into two separate keys: `brand.name_prefix` and `brand.name_suffix`.

**Before (`en.json`):**
```json
"brand": {
  "name": "Called & Equipped",
  "name_html": "Called <span class=\"text-synth-cyan\">&amp; EQUIPPED</span>"
}
```

**After (`en.json`):**
```json
"brand": {
  "name": "Called & Equipped",
  "name_prefix": "Called",
  "name_suffix": "& EQUIPPED"
}
```

This change was applied consistently across all locales (`en`, `es`, `fr`, `ru`).

### Frontend Updates
Updated `App.vue` to render the brand name using these new keys:

```vue
<!-- App.vue -->
<span class="font-display font-bold text-lg tracking-wider text-text-main">
  {{ $t('brand.name_prefix') }} <span class="text-synth-cyan">{{ $t('brand.name_suffix') }}</span>
</span>
```

### Test Configuration
- Updated `setupTests.js` to match the new i18n structure.
- Corrected the `defaultTitle` in `src/router/index.js` to "Called & Equipped".

## Files Created/Modified
- `frontend/src/router/index.js` - Updated default title.
- `frontend/src/locales/en.json` - Refactored brand name keys.
- `frontend/src/locales/es.json` - Refactored brand name keys.
- `frontend/src/locales/fr.json` - Refactored brand name keys.
- `frontend/src/locales/ru.json` - Refactored brand name keys.
- `frontend/src/App.vue` - Updated brand rendering logic.
- `frontend/src/setupTests.js` - Updated i18n mocks.

## Verification
- Ran `npx playwright test tests/e2e/sanity.spec.js` - **PASS**
- Ran `npx playwright test --workers=1 tests/e2e/production-readiness.spec.js` - **PASS** (verified key tests like admin access).
- Confirmed that the `[intlify]` HTML warning no longer appears in the console.
