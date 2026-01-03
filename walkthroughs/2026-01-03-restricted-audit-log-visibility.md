# Restricted Audit Log Visibility

**Date**: January 3, 2026
**Status**: Completed ✅

## Summary
Restricted the visibility of audit logs to users with `admin` or `super_admin` roles. This applies both to the backend API and the frontend user interface within the Organization settings.

## Issues Addressed
- **Unauthorized Data Access**: Prevents regular organization members from viewing sensitive audit history.
- **UI Clutter**: Hides the "Audit" tab for non-admin users in the organization settings page.

## Implementation Details
### Backend
- Modified `app/routers/audit.py` to include a role-based check in `get_audit_logs`.
- Added a 403 Forbidden response for users who are not administrators.
- Verified with a new automated test suite: `tests/test_audit_rbac.py`.

### Frontend
- Updated `OrganizationSettings.vue` to conditionally render the "Audit" tab based on both the organization's feature entitlements and the user's role (`isAdmin` or `isSuperAdmin`).

## Files Created/Modified
- `app/routers/audit.py` - Added role-based access logic.
- `frontend/src/pages/OrganizationSettings.vue` - Updated UI visibility logic.
- `tests/test_audit_rbac.py` - New RBAC verification tests.

## Verification
- **Automated Tests**: Ran `pytest tests/test_audit_rbac.py`. Both tests (blocking regular user, allowing admin) passed.
- **Manual Verification**: Verified `v-if` logic matches the intended role requirements.
