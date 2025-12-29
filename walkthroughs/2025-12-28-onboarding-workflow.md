# Onboarding & Notifications Workflow

**Date**: 2025-12-28
**Status**: Completed ✅

## Summary
Implemented a new onboarding flow for first-time users and added notification systems for administrators.
- **New Users**: Presented with a mandatory "First Run" modal to either join an organization or continue as an individual (which auto-joins 'neon-evangelion').
- **Admins**: Alerted to pending member approvals via a popup and a visual indicator in the user dropdown.

## Features Added

### 1. First Run Onboarding Modal
- **Trigger**: Appears for authenticated users who are not in an organization and haven't set a "Standalone Mode" preference.
- **Option A**: "Join or Create Organization" -> Redirects to Organization Settings.
- **Option B**: "Continue as Individual" -> Automatically attempts to join the default 'neon-evangelion' organization and sets the user's `standalone_mode` preference to true.

### 2. Admin Pending Member Alert
- **Trigger**: Appears for Admins when there are members with `membership_status: 'pending'` in their organization.
- **UI**: A non-intrusive popup in the bottom-right corner.
- **Action**: "Review Requests" button redirects to the Member list for approval.

### 3. Dropdown Indicator
- **Trigger**: Same as Admin Alert.
- **UI**: A red pulsing dot appears on the User Avatar dropdown button and next to the "Admin" (or Settings) link in the menu.

## Files Created/Modified
- `frontend/src/components/onboarding/OnboardingModal.vue` (New)
- `frontend/src/components/organization/AdminPendingAlert.vue` (New)
- `frontend/src/App.vue` (Modified to include modal, alert, and indicators)
- `frontend/src/stores/organization.js` (Added `joinNeonEvangelion` action)
- `frontend/src/locales/en.json` (Added translations)

## Verification
### Manual Verification Steps
1.  **Test New User Onboarding**:
    -   Create a new account (or clear local storage/user prefs).
    -   Login.
    -   Verify **Onboarding Modal** appears.
    -   Select "Continue as Individual".
    -   Verify user is redirected to Assessment and modal closes.
2.  **Test Admin Alerts**:
    -   Login as an Admin of an org with pending requests.
    -   Verify **Admin Alert** popup appears bottom-right.
    -   Verify **Red Dot** appears on the User Avatar in the top-right.
    -   Open dropdown -> Verify **Red Dot** appears next to "Admin" link.
    -   Click "Review Requests" on popup -> Verify redirection to Member Settings.
