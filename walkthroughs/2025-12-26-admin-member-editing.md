# Admin Member Editing Implementation

**Date**: December 26, 2025
**Status**: Completed ✅

## Summary
Implemented the ability for administrators to edit user roles and organization affiliations. This includes two levels of management:
1. **System Administrators**: Can edit any user's role, organization ID, and membership status via the Admin Dashboard.
2. **Organization Administrators**: Can edit the roles of members within their own organization.

## Issues Addressed / Features Added
- Added `UserUpdate` Pydantic schema for validation.
- Implemented `PATCH /admin/users/{user_id}` for system-wide user management.
- Implemented `PATCH /organizations/members/{user_id}` for organization-specific member management.
- Added `updateUser` and `updateMember` actions to Pinia stores.
- Created `UserEditModal.vue` for the Admin Dashboard.
- Added inline role editing to `MemberDetailModal.vue`.
- Updated `UserTable.vue` with edit actions and modal integration.
- Standardized localization strings for "Edit", "Save", "Cancel", and "Actions".

## Implementation Details

### Backend
- **Schemas**: New `UserUpdate` schema in `schemas.py` with optional `role`, `org_id`, and `membership_status` fields and field validators.
- **Admin Router**: Added a secure PATCH endpoint in `admin.py` that requires superuser privileges and logs changes via `AuditService`.
- **Organization Router**: Added a PATCH endpoint in `organizations.py` restricted to organization admins for updating member roles within their tenant.

### Frontend
- **Domain API**: Added `updateMember` to the organization domain service.
- **Stores**: Enhanced `AdminStore` and `OrganizationStore` to handle real-time state updates after a successful user modification.
- **UI Components**:
    - **`MemberDetailModal.vue`**: Added a toggleable edit state for the Role field, allowing organization admins to promote/demote members directly.
    - **`UserTable.vue`**: Added an "Actions" column with a pencil icon to trigger the edit modal.
    - **`UserEditModal.vue`**: A new high-fidelity modal using the project's glassmorphism aesthetic for global user management.

## Files Created/Modified
- `spiritual_gifts_backend/app/schemas.py` - Added `UserUpdate` schema
- `spiritual_gifts_backend/app/routers/admin.py` - Added user update endpoint
- `spiritual_gifts_backend/app/routers/organizations.py` - Added member update endpoint
- `spiritual-gifts/frontend/src/domain/organization/api.ts` - Added API call
- `spiritual-gifts/frontend/src/stores/admin.js` - Added `updateUser` action
- `spiritual-gifts/frontend/src/stores/organization.js` - Added `updateMember` action
- `spiritual-gifts/frontend/src/components/admin/UserEditModal.vue` - New component
- `spiritual-gifts/frontend/src/components/admin/UserTable.vue` - Integrated edit modal
- `spiritual-gifts/frontend/src/components/organization/MemberDetailModal.vue` - Added inline editing
- `spiritual-gifts/frontend/src/locales/en.json` - Updated translations

## Verification
- Verified backend schema validation for roles and statuses.
- Verified and logged audit entries for administrative updates.
- Manually checked UI responsiveness and glassmorphism styling of the new modal.
- Confirmed that organization admins cannot perform global user updates.
