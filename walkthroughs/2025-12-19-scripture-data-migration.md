# Scripture Data Migration

**Date**: December 19, 2025
**Status**: Completed ✅

## Summary
Migrated scripture reference data from a static frontend JS file to the backend API. This centralizes data management and allows for more efficient updates and potential database storage in the future.

## Issues Addressed / Features Added
- Removed static `scriptures.js` from the frontend.
- Created central `scriptures.json` in the backend.
- Exposed scripture data via a new `/scriptures` API endpoint.
- Implemented a Pinia store (`useScripturesStore`) to handle asynchronous fetching and caching.

## Implementation Details
### Backend
Added `load_scriptures` service and a new GET route in `routers.py`.
```python
@router.get("/scriptures")
def get_scriptures():
    return load_scriptures()
```

### Frontend
Created a Pinia store that uses the common axios `api` client.
```javascript
export const useScripturesStore = defineStore('scriptures', {
  actions: {
    async fetchScriptures() {
      const response = await api.get('/scriptures')
      this.scriptures = response.data
    }
  }
})
```

## Files Created/Modified
- `backend/app/data/scriptures.json` [NEW] - Centralized scripture data
- `backend/app/services/getJSONData.py` - Added scripture loading logic
- `backend/app/routers.py` - Exposed `/scriptures` endpoint
- `frontend/src/stores/scriptures.js` [NEW] - Pinia store for data fetching
- `frontend/src/components/ScripturePopover.vue` - Refactored to use Pinia store instead of local import
- `frontend/src/data/scriptures.js` [DELETE] - Removed legacy static file

## Verification
- Verified API endpoint with `curl`.
- Confirmed that `ScripturePopover.vue` correctly displays verses after the store initialization.
- Successful `npm run build` on the frontend.
