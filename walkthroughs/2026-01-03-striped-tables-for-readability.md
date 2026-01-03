# Striped Tables for Readability

**Date**: January 3, 2026
**Status**: Completed ✅

## Summary
Implemented global zebra-striping for all tables within the application to enhance readability and data scanning.

## Issues Addressed / Features Added
- Added global CSS rule for striped table rows.
- Improved readability of member, user, and audit log tables.

## Implementation Details
A global CSS rule was added to `main.css` targeting the even rows of all table bodies.

```css
/* Table Striping */
tbody tr:nth-child(even) {
  background-color: var(--color-bg-muted);
}
```

This uses the existing `--color-bg-muted` variable, which provides a better contrast against the base background across all themes (Midnight Sanctuary, Salt & Light, etc.).

## Files Created/Modified
- `frontend/src/assets/main.css` - Added global table striping rule.

## Verification
### Automated Tests
- Ran unit tests for components containing tables to ensure no regressions in layout or logic.
- `MemberDataTable.spec.js`: PASS
- `UserTable.spec.js`: PASS

### Manual Verification
- Verified the appearance in both dark and light themes.
- Confirmed that hover effects and selection highlights remain visible and distinct.
