# Super Admin Visibility & Access Fixes

**Date**: January 2, 2026
**Status**: Completed ✅

## Summary
Fixed an issue where users with the `super_admin` role were not correctly recognized as administrators in the frontend, leading to restricted navigation and missing features. Also improved the initial view for super admins to provide a broader system overview.

## Issues Addressed / Features Added
- **Global Audit Log View**: Added a new "Audit Logs" tab to the Leadership Overview for Super Admins to see all system-wide actions.
- **Enhanced Navigation**: Added `super_admin` identification to the sidebar and user dropdown badges.
- **Improved UX**: Changed the default tab for Super Admins in the Leadership Overview to "Members" (system-wide) instead of organization-specific settings.
- **Access to Insights**: Enabled the "Insights" tab for super admins regardless of organization plan limitations.
- **Audit Log Context**: Added "Organization" column to the Audit Log Table to provide context in global views.
- **i18n Support**: Added localized "Audit Logs" labels for English, Spanish, French, and Russian.
- **Updated Fallbacks**: Added `neon-ministry` to the list of organizations that automatically grant super admin privileges in the frontend.

## Implementation Details

### Frontend Access Control
Modified `src/domain/auth/guards.ts` and `src/stores/user.js` to ensure that any check for `isAdmin` now returns `true` for `super_admin` users. This preserves the "superset" role logic where super admins have all admin privileges and more.

### Sidebar and Dropdown
- Reordered role priority in `AppSidebar.vue` to correctly label "Super Admin" users.
- Updated `App.vue` to show a `ShieldCheckIcon` for the Leadership Overview and correctly badge the role.
- Added "Audit Logs" tab to `AppSidebar.vue` specifically for super admins in admin mode.

### Global Audit Logs
Updated `AuditLogTable.vue` to:
- Show an "Organization" column.
- Handle error states with user feedback.
- Support global fetching (no `orgId`) when used in the Admin Dashboard.
Updated `AdminDashboard.vue` to detect if the user is a `super_admin` and default their view to the "Members" list (global users) if no specific tab is requested. This prevents them from being stuck in the organization-specific "Settings" view which might be empty or redundant for them.

## Files Created/Modified
- `frontend/src/domain/auth/guards.ts` - Updated role checks and fallback slugs.
- `frontend/src/stores/user.js` - Updated `isAdmin` and `isSuperAdmin` getters.
- `frontend/src/stores/organization.js` - Updated `isAdmin` computed property.
- `frontend/src/pages/AdminDashboard.vue` - Adjusted default tab for super admins.
- `frontend/src/pages/OrganizationSettings.vue` - Enabled Insights for super admins.
- `frontend/src/App.vue` - Updated dropdown icons and role badges.
- `frontend/src/components/common/AppSidebar.vue` - Corrected role label priority.

## Verification
- Verified user role in database for `tonym415@gmail.com`.
- Manually checked role fallback slugs against database records.
- Verified that `super_admin` users now see "Leadership Overview" instead of "Member Settings" in the main dropdown.
