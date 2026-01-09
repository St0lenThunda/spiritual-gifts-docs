# Fix Cross-Site CSRF Validation Failure

**Date**: January 8, 2026
**Status**: Completed ✅

## Summary
Resolved a `400 Bad Request` (CSRF validation failed) error and relaxed rate limits to facilitate troubleshooting for cross-site authentication between Netlify and Render. This iteration adds a debug endpoint and centralized configuration for better stability.

## Issues Addressed / Features Added
- Fixed CSRF validation failures in cross-site production environments.
- Enabled `SameSite=None` and `Secure` attributes for CSRF cookies in production.
- Relaxed `/auth/send-link` rate limit further to `30/10minutes`.
- Enhanced CSRF failure logging with request headers and cookies.
- Added `/auth/debug-csrf` endpoint for detailed server-side diagnostics.

## Implementation Details
1.  **Centralized Configuration**: Moved `CsrfSettings` to `app/config.py` for more reliable initialization and explicit cookie naming (`fastapi-csrf-protect`).
2.  **Rate Limiting**: Increased the limit for the magic link endpoint to `30/10minutes` to avoid blocking developers during testing.
3.  **Enhanced Logging**: Maintained detailed verbose logging in the exception handler to show headers (Origin, Referer, X-CSRF-Token) and cookie presence.
4.  **Debug Endpoint**: Implemented a new POST endpoint `/auth/debug-csrf` that returns the current CSRF validation status and the raw headers/cookies received by the server without raising a 400 error.

## Files Created/Modified
- `app/config.py` - Centralized `CsrfSettings`.
- `app/main.py` - Updated to use centralized config and enhanced error logging.
- `app/routers/__init__.py` - Relaxed rate limits and added `/auth/debug-csrf`.

## Verification
- Deployed to Render and monitored logs for `csrf_violation` entries.
- Used the `/auth/debug-csrf` endpoint to cross-reference browser state with server perception.
