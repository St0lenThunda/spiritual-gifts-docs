# Onboarding Visibility Refinement

**Date**: December 30, 2025
**Status**: Completed ✅

## Summary
Refined the onboarding modal logic to ensure it only appears for new users who are not yet affiliated with an organization. This prevents unnecessary interruptions for administrators and established members who have already completed their setup.

## Issues Addressed / Features Added
- **Admins Excluded from Onboarding:** System and Organization admins will no longer see the onboarding popup, as their roles imply they are already past the initial setup phase.
- **Affiliated User Check:** Added a check for the `org_id` in the user profile to suppress the modal even if the organization store is still loading or partially initialized.
- **Enhanced Logic Reliability:** Combined multiple state checks (`isAuthenticated`, `isAdmin`, `org_id`, `hasOrganization`, and `standalone_mode`) into a single robust guard.

## Implementation Details

### Refined Logic in `App.vue`
Updated the `checkOnboarding` function to include administrative and organizational affiliation checks:
```javascript
const checkOnboarding = ( source = 'unknown' ) => {
  if (
    userStore.isAuthenticated &&
    !userStore.isAdmin && // Explicitly exclude admins
    !userStore.user?.org_id && // Safety check for user-level affiliation
    !organizationStore.hasOrganization &&
    !userStore.user?.global_preferences?.standalone_mode
  ) {
    showOnboarding.value = true;
  } else {
    showOnboarding.value = false;
  }
}
```

## Files Created/Modified
- `frontend/src/App.vue` - Implemented refined `checkOnboarding` logic.
- `frontend/package.json` - Added `test:record` script for easier Playwright codegen access.

## Verification
- **Verified for Admin:** Logged in as `tonym415@gmail.com` (Admin) and confirmed the onboarding modal did not appear.
- **Verified for Affiliated User:** Logged in as a user with an existing `org_id` and confirmed the modal stayed hidden.
- **CodeGen Validation:** Successfully used `playwright codegen` with `auth.json` to verify that recorded sessions correctly inherit the suppressed state.
