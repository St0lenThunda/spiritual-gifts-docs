# Role-Based Access Control: super_admin Support

**Date**: January 02, 2026
**Status**: Completed ✅

## Summary
Updated Pydantic schemas in the backend to explicitly support and validate the `super_admin` role. This prevents the API from rejecting or misidentifying accounts with system-level privileges during updates or invitations.

## Issues Addressed / Features Added
- Added support for `super_admin` role in `UserUpdate` schema.
- Added support for `super_admin` role in `OrganizationMemberInvite` schema.
- Added support for `super_admin` role in `OrganizationMemberUpdate` schema.
- Updated field descriptions across schemas to reflect the availability of the `super_admin` role.

## Implementation Details
Modified `app/schemas.py` to include `"super_admin"` in the `field_validator` logic for role fields. This ensures that Pydantic's strict validation doesn't throw errors when encountering this role.

Example change in `UserUpdate`:
```python
    @field_validator("role")
    @classmethod
    def validate_role(cls, v: Optional[str]) -> Optional[str]:
        if v is not None and v not in ["user", "admin", "super_admin"]:
            raise ValueError("Role must be 'user', 'admin', or 'super_admin'")
        return v
```

## Files Created/Modified
- `app/schemas.py` - Updated validation logic and field descriptions for user roles.

## Verification
Created a test script `test_rbac_schemas.py` that validates each modified schema with the `super_admin` role. The script confirmed that:
1. `UserUpdate` accepts `role="super_admin"`.
2. `OrganizationMemberInvite` accepts `role="super_admin"`.
3. `OrganizationMemberUpdate` accepts `role="super_admin"`.
4. Invalid roles are still correctly rejected.
