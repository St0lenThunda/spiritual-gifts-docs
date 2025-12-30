
# Fix Member Data Calculation

**Date**: December 29, 2025
**Status**: Completed ✅

## Summary
Fixed an issue where organization member data (Assessment Count and Top Gift) was incorrect for users who took assessments before joining the organization or without an organization context (`org_id=None`).

## Issues Addressed
- **Incorrect Member Data**: Users like `test@test.com` showed 0 assessments and no top gift in the Leadership Overview, despite having taken assessments.
- **Backend Filtering Logic**: The `list_organization_members` endpoint strictly filtered surveys by `org_id`, excluding valid historical or standalone assessments belonging to current members.

## Implementation Details

### Backend Logic Update
Modified `backend/app/routers/organizations.py` to fetch assessments by `user_id` inclusion rather than just `org_id`.

```python
# Before
org_surveys = db.query(Survey).filter(
    Survey.org_id == org.id
).order_by(Survey.created_at.desc()).all()

# After
member_ids = [m.id for m in members]
org_surveys = db.query(Survey).filter(
    Survey.user_id.in_(member_ids)
).order_by(Survey.created_at.desc()).all()
```

This ensures that all assessments belonging to the organization's members are counted, regardless of the context in which they were taken.

## Files Created/Modified
- `backend/app/routers/organizations.py` - Updated survey query logic.
- `backend/tests/test_organizations.py` - Added regression tests for calculation logic and historical surveys.
- `backend/debug_user_data.py` - (Temporary) Created for reproduction and verification.

## Verification

### Automated Tests
- Created `test_list_members_calculation_logic` to verify accurate counting of assessments and top gift determination.
- Created `test_list_members_includes_historical_surveys` to verify inclusion of surveys with `org_id=None`.
- Ran `pytest backend/tests/test_organizations.py` - **PASSED** (33 tests).

### Manual Verification
- Used `debug_user_data.py` to simulate the backend logic against the production data for `test@test.com`.
- **Pre-Fix**: Calculated Assessment Count: 0
- **Post-Fix**: Calculated Assessment Count: 1, Top Gift: Giving
