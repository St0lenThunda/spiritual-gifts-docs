# Debugging Denomination API Error

**Date**: December 31, 2025
**Status**: Completed ✅

## Summary
Resolved a 500 Internal Server Error on the `/api/v1/denominations/` endpoint caused by a mismatch between the running server's model and the database schema.

## Issues Addressed / Features Added
- Fixed 500 error on denominations API.
- Synchronized `Denomination` model and database schema.
- Resolved truncated environment variable issue (`STRIPE_PRICE_IDS`).

## Implementation Details
1. **Investigation**: Identified that the running server was querying for a `key` column which had been renamed to `slug` in the codebase.
2. **Analysis**: Discovered the running server was using outdated code (likely due to a stale reload state or environmental mismatch).
3. **Environment Fix**: Identified and resolved a truncated `STRIPE_PRICE_IDS` environment variable that was preventing configuration loading during migrations.
4. **Database Sync**: Created and applied an Alembic migration (`c4e138519de3`) to add missing `active_gift_keys` and `pastoral_overlays` columns to the `denominations` table.
5. **Server Refresh**: Restarted the backend server to ensure it loaded the current `app/models.py`.

## Files Created/Modified
- `alembic/versions/c4e138519de3_add_missing_denomination_columns.py` - New migration to sync schema.
- `.env` - Standardized `STRIPE_PRICE_IDS` formatting.

## Verification
- Verified the `/api/v1/denominations/` endpoint returns a successful response (empty list `[]` as expected for a fresh DB).
- Verified that the `inspect_db.py` diagnostic script (temporary) showed all expected columns in the database.
- Confirmed the server logs no longer show SQL `UndefinedColumn` errors.
