# Theme Sync on Login Fix

**Date**: December 31, 2025
**Status**: Completed ✅

## Summary
Resolved an issue where the organization's theme/branding did not apply immediately after a user logged in. The theme would only sync after visiting the organization settings or overview page.

## Issues Addressed / Features Added
- **Theme Sync on Login**: Organization branding and user preferences are now fetched and applied as soon as the user is authenticated.
- **Centralized Theme Resolution**: Moved all theme application logic into `userStore.resolveTheme`, which now serves as the single source of truth.
- **Standardized Branding Application**: Updated `organizationStore.applyBranding` to be exportable and used it for all theme changes, ensuring custom colors and presets are handled consistently.
- **Improved App Initialization**: Updated `App.vue` to proactively fetch organization data and user preferences on both mount and authentication changes.

## Implementation Details

### Centralized Logic
Previously, both `userStore` and `organizationStore` had separate, slightly different ways of applying themes to the DOM. I unified this by:
1. Making `applyBranding` in `organizationStore` the master function for DOM manipulation (handling both CSS classes and custom properties).
2. Updating `userStore.resolveTheme` to call `applyBranding` based on whether `theme_sync` is enabled.

### Authentication Flow Update
In `App.vue`, a new async watcher was added to trigger the necessary data fetches as soon as `isAuthenticated` becomes true. This ensures that even for fresh logins, the theme is applied before the user even reaches the dashboard.

```javascript
watch(
  [() => userStore.isAuthenticated, () => organizationStore.organization],
  async ( [isAuth, org] ) => {
    if ( isAuth ) {
      if ( !org ) {
        await Promise.all( [
          organizationStore.fetchOrganization(),
          userStore.loadPreferences()
        ] );
      }
      userStore.resolveTheme();
    }
  }
);
```

## Files Created/Modified
- `frontend/src/stores/organization.js`: Exported `applyBranding`, updated `fetchOrganization` to use `resolveTheme`.
- `frontend/src/stores/user.js`: Refactored `resolveTheme` and `applyTheme` to use `applyBranding`.
- `frontend/src/App.vue`: Updated `onMounted` and added a robust watcher to handle post-login data sync.
- `frontend/src/stores/__tests__/user-preferences.spec.js`: Updated unit tests to match class-based theme application.

## Verification
- **Unit Tests**: Ran `npm run test:unit user-preferences.spec.js` and confirmed all 16 tests pass.
- **Manual Verification**: Cross-checked the login flow (both Magic Link and Dev Login) to ensure `fetchCurrentUser` triggers the `App.vue` watcher, which now handles the rest of the sync.
