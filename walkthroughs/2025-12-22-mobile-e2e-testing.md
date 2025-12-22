# Mobile E2E Testing with Visual Regression

**Date**: December 22, 2025
**Status**: Completed ✅

## Summary

Implemented mobile-simulated E2E tests using Playwright with device emulation (Pixel 7 and iPhone 14) and visual regression testing via screenshot comparisons. This ensures the mobile experience remains premium as the codebase evolves.

## Issues Addressed / Features Added

- **Mobile Device Emulation**: Added Playwright projects for Pixel 7 (Android) and iPhone 14 (iOS)
- **Visual Regression Testing**: Screenshot comparison tests for key pages with configurable diff thresholds
- **Mobile Navigation Tests**: Hamburger menu, drawer behavior, touch target size validation
- **Mobile Assessment Tests**: Responsive layout, touch interactions, progress indicators
- **New NPM Scripts**: Dedicated commands for mobile and visual testing

## Implementation Details

### Playwright Configuration Updates

```javascript
// playwright.config.js
projects: [
  // Desktop browsers
  { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
  { name: 'firefox', use: { ...devices['Desktop Firefox'] } },
  { name: 'webkit', use: { ...devices['Desktop Safari'] } },
  
  // Mobile devices
  { name: 'mobile-chrome', use: { ...devices['Pixel 7'] } },
  { name: 'mobile-safari', use: { ...devices['iPhone 14'] } },
],

expect: {
  toHaveScreenshot: {
    maxDiffPixelRatio: 0.05, // Allow 5% pixel difference
    threshold: 0.2,           // Color difference threshold
  },
},
```

### New NPM Scripts

| Script | Description |
|--------|-------------|
| `test:e2e:mobile` | Run tests on Pixel 7 and iPhone 14 |
| `test:e2e:visual` | Run visual regression tests only |
| `test:e2e:update-snapshots` | Update baseline screenshots |

### Test Suites

1. **mobile-navigation.spec.js**: Tests hamburger menu visibility, drawer open/close, navigation accessibility, and WCAG touch target compliance (44x44px minimum)

2. **mobile-assessment.spec.js**: Tests assessment wizard on mobile including responsive question cards, touch-friendly answer buttons, progress visibility, and navigation

3. **visual-regression.spec.js**: Screenshot comparison tests for homepage, login, definitions, dashboard, assessment instructions, and key UI components

## Files Created/Modified

- `frontend/playwright.config.js` - Added mobile projects and visual regression settings
- `frontend/tests/e2e/mobile/mobile-navigation.spec.js` - Mobile navigation tests
- `frontend/tests/e2e/mobile/mobile-assessment.spec.js` - Mobile assessment flow tests
- `frontend/tests/e2e/mobile/visual-regression.spec.js` - Visual regression tests
- `frontend/package.json` - Added mobile/visual test scripts

## Verification

### Running Mobile Tests
```bash
# Run all mobile tests (Pixel 7 + iPhone 14)
npm run test:e2e:mobile

# Run visual regression tests
npm run test:e2e:visual

# Update baseline screenshots
npm run test:e2e:update-snapshots
```

### Requirements
- Backend must be running for authenticated tests (dev mode login)
- For iPhone 14/WebKit tests, ensure WebKit dependencies are installed:
  ```bash
  npx playwright install-deps webkit
  ```

### Screenshot Baselines
Visual regression tests will create baseline screenshots on first run in:
```
tests/e2e/mobile/visual-regression.spec.js-snapshots/
```

Subsequent runs compare against these baselines and flag visual differences.
