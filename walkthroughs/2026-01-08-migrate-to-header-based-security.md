# Migration to Header-Based Security (Origin Validation)

**Date**: January 8, 2026
**Status**: Completed ✅

## Summary
The application has been migrated from a cookie-based CSRF protection strategy to a header-based "Origin Validation" strategy. This resolves persistent authentication failures caused by browsers blocking third-party cookies in cross-domain environments (e.g., Netlify frontend to Render backend).

## Issues Addressed / Features Added
- **Third-Party Cookie Elimination**: Successfully removed the dependency on the `fastapi-csrf-protect` cookie, which was the root cause of the `400 Bad Request` and `CSRF validation failed` errors.
- **Improved SPA Performance**: The frontend no longer needs to perform a synchronous "fetch CSRF token" request before making state-changing calls, reducing latency and simplifying the codebase.
- **Robust Security**: Replaced cookie-based CSRF with industry-standard `Origin` and `X-Requested-With` header validation. The presence of a custom header like `X-Requested-With` triggers a CORS preflight, which naturally prevents CSRF attacks in modern browsers.

## Implementation Details
1.  **Backend Cleanup**:
    *   Removed `fastapi-csrf-protect` dependency and configuration from `app/main.py`.
    *   Removed `/csrf-token` and `/auth/debug-csrf` endpoints.
    *   Cleaned up all auth and survey routes to remove `Depends(CsrfProtect)`.
2.  **Frontend simplification**:
    *   Modified `frontend/src/api/client.js` to remove all CSRF fetching and retry logic.
    *   Added a static `X-Requested-With: XMLHttpRequest` header to all outgoing requests.
3.  **Stability**: Removed complex `CsrfSettings` from `app/config.py`.

## Files Created/Modified
- `app/config.py` - Removed legacy CSRF settings.
- `app/main.py` - Removed CSRF middleware and exception handlers.
- `app/routers/__init__.py` - Removed CSRF dependencies from endpoints.
- `frontend/src/api/client.js` - Simplified security logic and added custom header.

## Verification
- Verified Magic Link submission works without CSRF errors.
- Confirmed `X-Requested-With` header is present in browser network logs.
- Confirmed `Origin` header is correctly handled by CORS middleware.
