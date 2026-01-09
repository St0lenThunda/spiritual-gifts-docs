# Fix Cross-Site CSRF Validation Failure

**Date**: January 8, 2026
**Status**: Completed ✅

## Summary
Resolved a `400 Bad Request` (CSRF validation failed) error and relaxed rate limits to facilitate troubleshooting for cross-site authentication between Netlify and Render.

## Issues Addressed / Features Added
- Fixed CSRF validation failures in cross-site production environments.
- Enabled `SameSite=None` and `Secure` attributes for CSRF cookies in production.
- Relaxed `/auth/send-link` rate limit from `3/10minutes` to `10/10minutes`.
- Enhanced CSRF failure logging with request headers and cookies for better diagnostics.

## Implementation Details
1.  **CSRF Configuration**: Updated `CsrfSettings` in `app/main.py` to use `SameSite=None` and `Secure` in production.
2.  **Rate Limiting**: Increased the limit for the magic link endpoint in `app/routers/__init__.py` to allow more attempts during debugging.
3.  **Enhanced Logging**: Modified `csrf_protect_exception_handler` in `app/main.py` to log relevant headers (Origin, Referer, X-CSRF-Token) and cookie names, helping identify why validation fails.

## Files Created/Modified
- `app/main.py` - Updated `CsrfSettings` and enhanced error logging.
- `app/routers/__init__.py` - Relaxed rate limits for magic link sending.

## Verification
- Deployed to Render and monitored logs for `csrf_violation` entries.
- Confirmed that multiple login attempts can be made without hitting the `429` error prematurely.
