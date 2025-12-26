# SaaS Hardening Phase 1: Authentication Boundary

**Date**: December 25, 2025
**Status**: Completed ✅

## Summary
Established a "Hard Boundary" for backend authentication and multi-tenancy enforcement. Implemented strict entitlements handling and a rich `UserContext` to ensure every request is properly scoped to an organization.

## Issues Addressed / Features Added
- **Strict Organization Enforcement**: Replaced loose checks with `RequireOrg` (now `require_org`) dependency.
- **Rich Auth Context**: Introduced `UserContext` in `neon_auth.py` to eagerly load organization and permissions.
- **Entitlements Engine**: Created `app/services/entitlements.py` defining strict feature limits for Free, Individual, Ministry, and Church plans.
- **Route Hardening**: Audited and refactored `app/routers/organizations.py` to use strict dependency injection.

## Implementation Details

### 1. Entitlements Service
Created `app/services/entitlements.py` to serve as the single source of truth for tier limits.
```python
TIER_FEATURES = {
    Plan.FREE: { "users": 10, ... },
    Plan.MINISTRY: { "users": 100, ... }
}
```

### 2. UserContext & RequireOrg
Refactored `app/neon_auth.py` to provide a robust authentication context.
- **UserContext**: Pydantic model wrapping User, Organization, Role, and Permissions.
- **require_org**: Function-based dependency that enforces active organization membership.
- **get_user_context**: Replaced `get_current_user` logic to build the context eagerly.

### 3. Route Refactor
Updated `app/routers/organizations.py` to use `Depends(require_org)` instead of local checks.

## Files Created/Modified
- `app/services/entitlements.py` - [NEW] Tier definitions.
- `app/neon_auth.py` - [MODIFY] implementation of UserContext and require_org.
- `app/routers/organizations.py` - [MODIFY] Adopted strict dependencies.
- `tests/test_branding.py` - [MODIFY] Updated to use new overrides.
- `tests/test_organizations.py` - [MODIFY] Updated to use `require_org` and `UserContext` overrides.

## Verification
- **Automated Tests**:
  - `pytest tests/test_branding.py`: Verified styling settings persist under new auth.
  - `pytest tests/test_organizations.py`: Verified 31 organization/member management scenarios.
  - Confirmed Pydantic validation handles mocked `Organization` objects correctly.
