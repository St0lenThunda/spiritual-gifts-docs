# Data Normalization & Unified Source of Truth

**Date**: December 21, 2024
**Status**: Completed ✅

## Summary
Successfully implemented data normalization across the stack by reconciling gift identifiers and mappings. Previously, the backend used a hardcoded scoring map that was out of sync with the frontend's question data. Now, the backend dynamically builds its scoring logic directly from `questions.json`, ensuring complete consistency.

## Issues Addressed
- **Backend Mapping Bug**: The hardcoded `GIFT_MAPPINGS` in `SurveyService` used a 16-gift model that attributed questions incorrectly compared to the actual text in `questions.json`.
- **Inconsistent Naming**: "Service" was used in some files while "Service/Helps" was used in others. "Shepherding" conflicted with "Pastor/Shepherd".
- **Source of Truth Conflict**: Changes to `questions.json` (e.g., adding or reordering questions) would have required manual, error-prone updates to the backend service.

## Implementation Details

### 1. Unified Naming
Standardized all spiritual gift identifiers across the system to match `gifts.json`:
- **Service** → **Service/Helps**
- **Shepherding** → **Pastor/Shepherd**

### 2. Dynamic Backend Mapping
Refactored `SurveyService` to remove hardcoded constants and instead build its internal mapping at runtime from the authoritative `questions.json` file.

```python
@classmethod
def get_gift_mappings(cls) -> Dict[str, List[int]]:
    if cls._gift_mappings is None:
        data = load_questions()
        mappings = {}
        for q in data["assessment"]["questions"]:
            gift = q["gift"]
            if gift not in mappings:
                mappings[gift] = []
            mappings[gift].append(q["id"])
        cls._gift_mappings = mappings
    return cls._gift_mappings
```

### 3. Synchronized Data
Ensured `questions.json` accurately reflects the desired 10-gift assessment model (8 questions per gift, 80 total), while maintaining definitions for all 20 historical gifts in `gifts.json` for future expansion.

## Files Modified
- `spiritual_gifts_backend/app/data/questions.json`: Harmonized names.
- `spiritual_gifts_backend/app/services/survey_service.py`: Refactored for dynamic mapping.
- `spiritual-gifts/frontend/src/data/questions.json`: Mirrored changes for frontend consistency.
- `spiritual_gifts_backend/tests/test_services.py`: Updated tests to align with 10-gift model.

## Verification
- Ran full test suite: `pytest` passed (59 tests passed, 0 failures).
- Verified `calculate_scores` logic with mock data matching the new `questions.json` structure.
- Coverage for `SurveyService` remains at 100%.
