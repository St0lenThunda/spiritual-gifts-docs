# Test and Lint Fixes

**Date**: December 22, 2025
**Status**: Completed ✅

## Summary

Fixed all pytest warnings (backend), ESLint errors (frontend), and test failures to enable a clean `npm run validate` workflow that runs lint → unit tests → production build.

## Issues Addressed

- **DeprecationWarning** (4 occurrences): Starlette TestClient deprecated per-request `cookies=` parameter
- **RuntimeWarning** (1 occurrence): Import order issue with `runpy.run_module()` for `app.services.getJSONData`
- **MovedIn20Warning** (1 occurrence): Using deprecated SQLAlchemy `declarative_base()` import path
- **Duplicate import**: `pytest` was imported twice in `test_coverage_gap_closers.py`

## Implementation Details

### Cookie Deprecation Fixes

The Starlette TestClient now prefers setting cookies directly on the client instance instead of per-request. Changed from:

```python
response = client.get("/api/v1/auth/me", cookies={"access_token": token})
```

To:

```python
client.cookies.set("access_token", token)
response = client.get("/api/v1/auth/me")
client.cookies.clear()
```

### RuntimeWarning Suppression

For the `test_get_json_data_main` test that uses `runpy.run_module()` to hit the `__main__` block, added a pytest marker to ignore the expected warning:

```python
@pytest.mark.filterwarnings("ignore::RuntimeWarning")
def test_get_json_data_main():
    import runpy
    with patch("builtins.print"):
        runpy.run_module("app.services.getJSONData", run_name="__main__")
```

### SQLAlchemy Import Update

Updated the import path from the deprecated location to the new SQLAlchemy 2.0 location:

```diff
-from sqlalchemy.ext.declarative import declarative_base
+from sqlalchemy.orm import declarative_base
```

## Files Modified

### Backend
- [`test_auth_ext.py`](file:///home/thunda/Dev/543_Tools/spiritual_gifts_backend/tests/test_auth_ext.py) - Fixed 3 cookie deprecation warnings
- [`test_router_coverage.py`](file:///home/thunda/Dev/543_Tools/spiritual_gifts_backend/tests/test_router_coverage.py) - Fixed 1 cookie deprecation + improved test logic
- [`test_coverage_edge_cases.py`](file:///home/thunda/Dev/543_Tools/spiritual_gifts_backend/tests/test_coverage_edge_cases.py) - Suppressed expected RuntimeWarning
- [`test_coverage_gap_closers.py`](file:///home/thunda/Dev/543_Tools/spiritual_gifts_backend/tests/test_coverage_gap_closers.py) - Updated SQLAlchemy import, removed duplicate import

### Frontend
- [`vitest.config.js`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/vitest.config.js) - Added `tests/e2e/**` to exclude pattern

---

## Frontend: Vitest Configuration Fix

### Issue
Vitest was incorrectly trying to run Playwright E2E test files located in `tests/e2e/`, causing 5 test failures with errors like:
```
Error: Playwright Test did not expect test() to be called here.
```

### Fix
Updated [`vitest.config.js`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/vitest.config.js) to exclude the correct E2E test path:

```diff
-exclude: [...configDefaults.exclude, 'e2e/**'],
+exclude: [...configDefaults.exclude, 'e2e/**', 'tests/e2e/**'],
```

### Verification

**Unit Tests (Vitest):**
```
Test Files  12 passed (12)
     Tests  51 passed (51)
  Duration  8.35s
```

**E2E Tests (Playwright):**
```
Total: 100 tests in 5 files
```

---

## Verification

**Backend Tests:**
```
76 passed in 2.74s (0 warnings)
```

**Frontend `npm run validate`:**
```
✓ lint: 0 errors
✓ test:unit: 12 files, 51 tests passed  
✓ build:safe: built in 1m 50s
```

---

## ESLint Fixes (20 errors)

### Config Updates
- [`eslint.config.js`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/eslint.config.js) - Added node globals for config files, disabled multi-word-component-names for pages

### Unused Variables Removed
- [`AssessmentHistory.vue`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/components/dashboard/AssessmentHistory.vue) - Removed unused `computed` import
- [`StatsCard.vue`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/components/dashboard/StatsCard.vue) - Removed unused `computed` and `props` variable
- [`Dashboard.vue`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/pages/Dashboard.vue) - Removed unused `useRouter` import
- [`Login.vue`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/pages/Login.vue) - Removed unused `verifyMagicToken` and `response`
- [`TakeAssessment.vue`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/pages/TakeAssessment.vue) - Removed unused `shortQuestions`
- [`router/index.js`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/router/index.js) - Removed unused `metaDescriptionTag`
- [`survey.js`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/stores/survey.js) - Removed unused `calculateGiftScores`
- [`survey.spec.js`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/stores/__tests__/survey.spec.js) - Removed unused `mockQuestionsResponse`

### Test Fixes
- [`user.spec.js`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/src/stores/__tests__/user.spec.js) - Fixed vitest expect assertion and added proper console.error spy

---

## New NPM Scripts

Added to [`package.json`](file:///home/thunda/Dev/543_Tools/spiritual-gifts/frontend/package.json):

| Script | Command | Purpose |
|--------|---------|---------|
| `test:all` | `vitest --run && npm run build:safe` | Unit tests + build validation |
| `validate` | `npm run lint && npm run test:all` | Full validation pipeline |
