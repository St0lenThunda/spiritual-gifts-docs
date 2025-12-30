# Default Organization Selection

**Date**: December 29, 2025
**Status**: Completed ✅

## Summary
Changed the default tab selection to "Organization" for both the User Settings page and the Leadership Overview (Admin Dashboard).

## Issues Addressed
- Users were previously defaulted to "Appearance" in Settings.
- Admins were previously defaulted to "Logs" in Leadership Overview.
- "Organization" is now the primary focus when navigating to these sections.

## Implementation Details
- **Settings.vue**: Updated `activeTab` ref to initialize as `'organization'`.
- **admin.js**: Updated `state.activeTab` to initialize as `'organization'`.

## Files Created/Modified
- `frontend/src/pages/Settings.vue` - Changed default tab.
- `frontend/src/stores/admin.js` - Changed default tab.
- `frontend/src/pages/__tests__/Settings.spec.js` - Added unit test for default tab.
- `frontend/src/stores/__tests__/admin.spec.js` - Added unit test for default tab.

## Verification
### Automated Tests
Ran `npx vitest run src/pages/__tests__/Settings.spec.js src/stores/__tests__/admin.spec.js`

**Results**:
- `Settings.spec.js`: Verified `activeTab` defaults to 'organization'.
- `admin.spec.js`: Verified store state defaults to 'organization'.
