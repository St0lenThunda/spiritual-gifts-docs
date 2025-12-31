# Pastoral Tone Alignment for Notifications

**Date**: May 22, 2024
**Status**: Completed ✅

## Summary
Refined application notification messages (toasts) and UI text to reflect a pastoral, supportive, and mission-aligned tone. This involved updating `en.json` with warmer language and ensuring consistency across Spanish, French, and Russian locales. Missing keys used in the codebase were also added to prevent UI regressions.

## Issues Addressed / Features Added
- Infuse notifications with pastoral language (Grace, Peace, Guidance).
- Add missing i18n keys for better UI rendering (`create_success`, `member_waiting_approval`).
- Fix corrupted or missing keys in non-English locales.
- Centralize and pastoralize system error messages in `client.js`.
- Update unit tests to align with recent UI structure changes.

## Implementation Details
Updated all four locale JSON files in `frontend/src/locales/`. The rephrasing focused on moving away from clinical "Success" messages toward celebratory and welcoming language. Additionally, the `frontend/src/api/client.js` was refactored to use localized keys for all interceptor-level errors.

## Files Created/Modified
- `frontend/src/locales/en.json`, `es.json`, `fr.json`, `ru.json`
- `frontend/src/api/client.js`
- `frontend/src/components/organization/__tests__/MemberDataTable.spec.js`

## Verification
- Ran full suite of frontend unit tests: `npm run test:unit` (182/182 passed).
- Manual verification of key rendering in `MemberDetailModal.vue` and `OrganizationSettings.vue`.
