# Fixes: Billing 500, Org Join, Theme Sync

**Date**: December 28, 2025
**Status**: Completed ✅

## Summary
Addressed multiple critical bugs affecting the organization join flow ("Continue as Individual"), billing status retrieval (500 Error), and user preference persistence (Theme Sync).

## Issues Addressed / Features Added
- **Fixed Billing 500 Error**: Backend `get_plan_features` crashed on `None` input; added fallback to Free plan.
- **Fixed Standalone Mode**: "Continue as Individual" logic in `App.vue` lacked error handling and targeted a non-default organization (`neon-evangelion` vs `neon-ministry`).
- **Fixed Theme Persistence**: 
    - Added missing `theme_sync` field to backend schemas.
    - Updated `applyTheme` in frontend to correctly prefix CSS classes with `theme-`.
- **User Migration**: Migrated `test@test.com` to the correct organization (`neon-ministry`) to align with admin.

## Implementation Details

### Backend
- **Entitlements**: Hardened `get_plan_features` in `services/entitlements.py`.
- **Schemas**: Added `theme_sync` to `PreferenceUpdate` and `UserPreferences` in `schemas.py`.

### Frontend
- **App.vue**: Added `try/catch` block to `handleJoinIndividual`.
- **Store**: Updated `useOrganizationStore` to defaulting to `neon-ministry`.
- **Store**: Updated `useUserStore` to prepend `theme-` to applied classes in `applyTheme`.

## Files Created/Modified
- `backend/app/services/entitlements.py` - Added `None` check for plan name.
- `backend/app/schemas.py` - Added `theme_sync` field.
- `frontend/src/App.vue` - Robust error handling for join flow.
- `frontend/src/stores/organization.js` - Updated default org slug.
- `frontend/src/stores/user.js` - Fixed CSS class application logic.

## Verification
- **Manual**: Verified "Continue as Individual" works without hanging.
- **Manual**: Verified Theme selection updates UI immediately and persists on refresh.
- **Script**: Verified user migration with `debug_check_users.py`.
