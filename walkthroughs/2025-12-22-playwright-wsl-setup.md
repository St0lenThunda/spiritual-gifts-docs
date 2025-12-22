# Playwright E2E Testing in WSL

**Date**: December 22, 2025
**Status**: Completed ✅

## Summary

Configured Playwright end-to-end testing to work reliably in WSL (Windows Subsystem for Linux) with VcXsrv for headed/UI modes, fixed browser dependency issues, and improved the assessment test spec to handle authentication.

## Issues Addressed / Features Added

- **X11 Display Configuration**: Playwright headed/UI modes failed with "Missing X server or $DISPLAY" errors
- **WebKit Missing Dependencies**: WebKit browser lacked system libraries (`libevent`, `libgstcodecparsers`, `libavif`)
- **VcXsrv Access Control**: X server blocked connections from WSL due to access control settings
- **Test Authentication**: Assessment spec failed because the `/take` route requires login
- **Skipped Tests**: `assessment.spec.js` used `test.skip()` making it invisible in UI mode

## Implementation Details

### WSL Display Configuration

Added dynamic DISPLAY variable to `~/.zshrc`:

```bash
# WSL X11 Display Configuration for GUI apps (e.g., Playwright)
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0
```

This dynamically resolves the Windows host IP each session.

### VcXsrv Configuration

VcXsrv must be launched with these settings:
1. **Display number**: `0`
2. **Disable access control**: ✅ **REQUIRED** - otherwise WSL connections are blocked
3. Allow VcXsrv through Windows Firewall for both Private and Public networks

### New NPM Scripts

Added WSL-friendly test commands to `package.json`:

```json
{
  "test:e2e": "npx playwright test",
  "test:e2e:ui": "npx playwright test --ui",
  "test:e2e:headed": "npx playwright test --headed --project=chromium",
  "test:e2e:debug": "npx playwright test --headed --project=chromium --debug",
  "test:e2e:report": "npx playwright show-report"
}
```

### Assessment Spec Improvements

Rewrote `assessment.spec.js` with:
- **Login helper function** for dev mode authentication
- **`beforeEach` hook** to ensure authenticated state before each test
- **`test.describe` block** for better test organization

```javascript
// Helper to perform dev mode login
async function loginWithDevMode( page, email = 'user@example.com' ) {
  await page.goto( '/login' );
  await page.fill( 'input[type="email"]', email );
  await page.click( 'button:has-text("LOGIN (DEV MODE)")' );
  await expect( page ).toHaveURL( /dashboard/, { timeout: 10000 } );
}

test.describe( 'Assessment Flow', () => {
  test.beforeEach( async ( { page } ) => {
    await loginWithDevMode( page );
  } );

  test( 'Basic Navigation', async ( { page } ) => {
    // Test implementation...
  } );
} );
```

### WebKit Dependencies

Installed missing WebKit libraries:

```bash
npx playwright install-deps webkit
```

## Files Created/Modified

- `frontend/package.json` - Added new e2e test scripts
- `frontend/tests/e2e/assessment.spec.js` - Rewrote with authentication flow
- `~/.zshrc` - Added DISPLAY environment variable for WSL

## Verification

All 6 E2E tests now pass:

```
Running 6 tests using 1 worker
  6 passed (53.7s)
```

**Test commands verified:**
- `npm run test:e2e` - Headless mode ✅
- `npm run test:e2e:headed` - Headed Chromium ✅
- `npm run test:e2e:debug` - Debug mode with Playwright Inspector ✅
- `npm run test:e2e:report` - HTML report viewer ✅
- `npm run test:e2e:ui` - Interactive UI mode (requires VcXsrv) ✅
