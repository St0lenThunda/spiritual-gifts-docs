# Resolve Authentication Flow 404/500 Errors

**Date**: January 9, 2026
**Status**: Completed ✅

## Summary
Resolved a persistent `404 Not Found` for `/api/v1/csrf-token` and a `500 Internal Server Error` for `/api/v1/auth/send-link`. The 404 was due to stale Netlify build artifacts, while the 500 was caused by a non-resolvable global Neon Auth URL.

## Issues Addressed / Features Added
-   **Fixed Stale Frontend Build**: Forced a clean rebuild on Netlify by making a trivial change to `client.js`. This purges the legacy CSRF-fetching logic from the production bundle.
-   **Fixed Neon Auth Connectivity**: Updated the backend to use the correct project-specific Neon Auth URL (`auth.silent-unit-86239189.us-east-1.aws.neon.tech`), resolving the `ConnectError` seen in backend logs.

## Implementation Details
The backend configuration was updated to allow for environment-specific `NEON_AUTH_URL` values, defaulting to the verified project-specific URL. The frontend received a trivial comment update to trigger a fresh deployment on Netlify, ensuring all legacy logic is removed from the served assets.

## Files Created/Modified
-   `app/config.py` (Backend) - Added `NEON_AUTH_URL` setting.
-   `app/neon_auth.py` (Backend) - Updated to use dynamic `NEON_AUTH_URL`.
-   `src/api/client.js` (Frontend) - Added rebuild trigger comment.

## Verification
-   **Backend Integration Tests**: `pytest tests/test_neon_integration.py` passed (4/4).
-   **DNS Resolution**: Verified `auth.silent-unit-86239189.us-east-1.aws.neon.tech` resolves correctly.
-   **Source Review**: Confirmed no remaining `/csrf-token` references in the `src` directory.
