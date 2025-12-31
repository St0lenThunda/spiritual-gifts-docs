# Multi-Denominational Support Implementation

**Date**: December 31, 2025
**Status**: Completed ✅

## Summary
Implemented the backend and frontend infrastructure to support multiple theological denominations ("Theological Profiles"). This transition moves the platform from a single-denomination model to a "Shared Core, Configured Interpretations" architecture (Model C), allowing organizations to select their preferred theological framework.

## Issues Addressed / Features Added
- **Backend Data Model**: Added `Denomination` and `ScriptureSet` tables; updated `Organization` to link to a denomination.
- **Service Layer**: Created `DenominationService` to manage profile data.
- **API**: New `/api/v1/denominations` router for fetching and managing profiles.
- **Frontend State**: Added `DenominationStore` (Pinia) to manage the active theological context.
- **Admin UI**: Integrated `DenominationSelector` into the "Organization Settings" > "General" tab, enabling admins to switch their organization's profile.
- **Super Admin UI**: Created a dedicated "Theological Profiles" management interface for system administrators to create/edit denominations and scripture sets.
- **Sidebar Separation**: Implemented visual and logical separation between Standard Admin and System Admin features in the dashboard sidebar.
- **Internationalization**: Added `denomination` namespace to i18n files for dynamic terminology.

## Implementation Details

### Backend
- **Migrations**: Alembic revision added tables for denominations and scripture sets.
- **Seeding**: Script created to seed the default "Spiritual Gifts" profile to ensure backward compatibility.
- **Security**: Denomination creation restricted to system admins; selection restricted to organization admins.

### Frontend
- **Component**: `DenominationSelector.vue` is an ARIA-compliant select component that fetches available profiles and updates the organization's setting.
- **Page**: `DenominationManagement.vue` provides a CRUD interface for managing denominations and scripture override sets (JSON editor).
- **Navigation**: Updated `AdminSidebar.vue` to support separate "System" section for super admins (pink vs cyan).
- **Integration**: Added to `OrganizationSettings.vue` below the usage statistics per profile.

## Files Created/Modified
- `backend/app/models.py` - Added models.
- `backend/app/schemas.py` - Added Pydantic schemas.
- `backend/app/routers/denominations.py` - New API endpoints.
- `backend/app/services/denomination_service.py` - Business logic.
- `frontend/src/stores/denomination.js` - Pinia store.
- `frontend/src/components/DenominationSelector.vue` - UI component.
- `frontend/src/pages/OrganizationSettings.vue` - UI integration.
- `frontend/src/locales/en.json` - Added translation keys.
- `frontend/src/pages/DenominationManagement.vue` - New Super Admin page.
- `frontend/src/components/admin/AdminSidebar.vue` - Updated navigation logic.
- `frontend/src/pages/AdminDashboard.vue` - Added tab content.

## Verification
- **Unit Tests**: Added `backend/tests/test_denominations.py` and `frontend/src/components/__tests__/DenominationSelector.spec.js`.
- **Integration Tests**: Added `backend/tests/test_content_override.py` to verify scripture override logic in `ContentService`.
- **Manual Verification**: Verified that the selector appears in the admin dashboard, loads available options, and successfully updates the organization's configuration upon selection.
