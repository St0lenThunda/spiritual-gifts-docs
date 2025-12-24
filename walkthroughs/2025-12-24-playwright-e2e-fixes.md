# Playwright E2E Fixes

**Date**: December 24, 2025
**Status**: Completed ✅

## Summary
Successfully debugged and fixed a series of failures in the Playwright E2E test suite affecting both desktop and mobile assessment flows. The issues ranged from backend runtime errors to frontend race conditions and fragile test locators.

## Issues Addressed / Features Added
- **Fixed Backend Caching Error**: Resolved a 500 error on the `/api/v1/questions` endpoint caused by a string/bytes mismatch in `fastapi-cache2` when using the `InMemoryBackend`.
- **Resolved Frontend Race Condition**: Replaced a hardcoded `setTimeout` in `TakeAssessment.vue` with an `await` on the store's question loading action, ensuring data is present before rendering.
- **Improved Test Reliability**: Migrated fragile text-based locators to robust class-based selectors (`.question-card-header`, `.progress-text`).
- **Enhanced Accessibility**: Added ARIA roles (`button`, `tabindex`) to assessment options in `QuestionCard.vue` to improve both E2E testability and user experience.
- **Fixed Syntax Errors**: Corrected a malformed CSS selector in `mobile-navigation.spec.js`.

## Implementation Details

### Backend: Caching Fix
Created a `SafeJsonCoder` in `app/routers/__init__.py` that handles both `str` and `bytes` inputs, preventing the `AttributeError: 'str' object has no attribute 'decode'` error standard to `fastapi-cache2` with non-Redis backends.

```python
class SafeJsonCoder(JsonCoder):
    @classmethod
    def decode(cls, value: Any) -> Any:
        if isinstance(value, str):
            return json.loads(value)
        return super().decode(value)
```

### Frontend: Loading Logic
Updated `TakeAssessment.vue` to correctly wait for data:
```javascript
// Ensure questions are loaded if they haven't been yet
if ( !questions.value.length ) {
  await store.loadQuestions();
}
loading.value = false;
```

## Files Created/Modified
- `spiritual_gifts_backend/app/routers/__init__.py` - Implemented `SafeJsonCoder` and updated `@cache` decorators.
- `frontend/src/pages/TakeAssessment.vue` - Fixed async loading logic.
- `frontend/src/components/assessment/QuestionCard.vue` - Added CSS classes and ARIA roles.
- `frontend/src/components/assessment/ProgressHeader.vue` - Added `.progress-text` class.
- `frontend/tests/e2e/assessment.spec.js` - Updated for robust locators.
- `frontend/tests/e2e/mobile/mobile-assessment.spec.js` - Updated for robust locators.
- `frontend/tests/e2e/mobile/mobile-navigation.spec.js` - Fixed syntax error.

## Verification
- **Full E2E Run**: Executed `npm run test:e2e:save` with a 100% pass rate across 18 tests.
- **Backend Validation**: Confirmed `/api/v1/questions` returns 200 OK after the fix.
- **Visual Regression**: Verified all 6 mobile visual regression tests pass.
