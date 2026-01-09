# Fix Cross-Site CSRF Validation Failure

**Date**: January 8, 2026
**Status**: Completed ✅

## Summary
Resolved a `400 Bad Request` (CSRF validation failed) error that prevented users from logging in when accessing the Render-hosted backend from the Netlify-hosted frontend.

## Issues Addressed / Features Added
- Fixed CSRF validation failures in cross-site production environments.
- Enabled `SameSite=None` and `Secure` attributes for CSRF cookies in production.

## Implementation Details
The `fastapi-csrf-protect` library by default uses `SameSite=Lax`, which prevents cookies from being sent in cross-site POST requests. To support the cross-domain setup (Netlify frontend -> Render backend), I updated the `CsrfSettings` in `app/main.py` to:
1.  Set `cookie_samesite="none"` in production.
2.  Ensure `cookie_secure=True` is always active in production to satisfy the `SameSite=None` requirement.

## Files Created/Modified
- `app/main.py` - Updated `CsrfSettings` configuration.

## Verification
- Manual verification requires deploying to Render and testing the login flow from the Netlify frontend.
- Inspected the code to ensure `settings.ENV` is used correctly to distinguish between development and production.
