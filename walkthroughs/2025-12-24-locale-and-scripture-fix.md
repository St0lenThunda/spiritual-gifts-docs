# Fix Survey Locale Updates and Scripture Popovers

**Date**: December 24, 2025
**Status**: Completed ✅

## Summary
Fixed two critical issues:
1. Survey questions were not updating when the application language was changed.
2. Scripture popovers on the Gifts page were not populating with data.

## Issues Addressed
- **Survey Locale Check**: Survey questions now reload immediately upon mounting and whenever the locale changes.
- **Missing Data**: Restored the missing `scriptures.json` file which powers the scripture popovers.
- **Testing**: Added unit tests to verify the fixes and ensure no regression.

## Implementation Details

### Survey Locale Fix
Modified `frontend/src/pages/TakeAssessment.vue` to use a watcher on `i18n.global.locale.value` with `{ immediate: true }`. This ensures:
- Questions are loaded on initial mount (using the current locale).
- Questions are re-fetched from the backend whenever the user switches language.

### Scripture Popovers Fix
Investigated the `ScripturePopover` component and found that the backend `/api/v1/scriptures` endpoint was relying on `data/scriptures.json` which was missing.
- Re-created `backend/data/scriptures.json` with KJV, NIV, and ESV text for all spiritual gift references.
- Verified that `frontend/src/stores/scriptures.js` correctly parses and serves this data.

## Files Created/Modified

### Frontend
- `src/pages/TakeAssessment.vue` - Updated watcher logic.
- `src/setupTests.js` - Added mocks for global i18n and ResizeObserver.
- `src/pages/__tests__/TakeAssessment.spec.js` - Added unit test for locale switching.
- `src/pages/__tests__/Gifts.spec.js` - Added unit test for scripture popover rendering.

### Backend
- `data/scriptures.json` - Restored file with Bible verses.

## Verification
- **Unit Tests**: Ran `npm run test:unit src/pages/__tests__/TakeAssessment.spec.js` and `npm run test:unit src/pages/__tests__/Gifts.spec.js` - both passed.
- **Manual Check**: Verified code logic ensures data fetching on appropriate triggers.
