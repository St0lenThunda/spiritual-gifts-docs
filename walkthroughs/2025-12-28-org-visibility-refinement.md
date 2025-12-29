# Organization Visibility Refinement

**Date**: 2025-12-28
**Status**: Completed ✅

## Summary
Updated the visibility logic for organization-related settings. The "Organization" settings tab is now accessible to all members, but the "Members" management section within it is restricted to admins. Additionally, a user's organization affiliation is now displayed in their Account Settings.

## Issues Addressed
- **Sidebar Visibility**: The "Organization" link in the settings sidebar was previously admin-only. It is now visible to all users.
- **Member List Privacy**: Non-admin members could see the member list. This is now hidden for non-admins.
- **Affiliation Display**: Users couldn't easily see which organization they belonged to in their account profile. Added organization name display.

## Implementation Details

### Frontend
- **`SettingsSidebar.vue`**:
    - Removed `if (userStore.isAdmin)` check for adding the Organization tab. Matches: `SettingsSidebar.vue`
- **`OrganizationSettings.vue`**:
    - Added `v-if="isAdmin"` to the "Members" tab button to hide it from non-admins.
- **`Settings.vue`**:
    - Imported `useOrganizationStore`.
    - Added a logic to fetch organization data `onMounted` if not already present.
    - Added a paragraph in the "Account" tab to display `orgStore.organization.name`.

## Files Modified
- `frontend/src/components/settings/SettingsSidebar.vue`
- `frontend/src/pages/OrganizationSettings.vue`
- `frontend/src/pages/Settings.vue`

## Verification
### Manual Verification Steps
1. **As Admin**:
    - Verify "Organization" tab exists in Settings Sidebar.
    - Verify "Members" tab exists in Organization Settings.
    - Verify Organization Name appears in "Account" tab.
2. **As Member (Non-Admin)**:
    - Verify "Organization" tab exists in Settings Sidebar.
    - Verify "Members" tab is **HIDDEN** in Organization Settings.
    - Verify Organization Name appears in "Account" tab.
