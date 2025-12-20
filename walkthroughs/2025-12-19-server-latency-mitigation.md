# Server Latency Mitigation

**Date**: December 19, 2025
**Status**: Completed ✅

## Summary
Implemented a multi-layered strategy to address cold-start delays on the Render free tier, improving perceived performance and system reliability.

## Issues Addressed / Features Added
- Reduced cold-start delays by adding a lightweight `/health` endpoint.
- Optimized backend startup by deferring database initialization to a lifespan event.
- Implemented a Netlify-based warm-up ping system.
- Added automatic retry logic in the frontend for 5xx errors and timeouts.
- Implemented a "Waking Server" UI notification for better user feedback.

## Implementation Details
### Backend Optimizations
Moved `Base.metadata.create_all` into a lifespan async context manager.
```python
@asynccontextmanager
async def lifespan(app: FastAPI):
    Base.metadata.create_all(bind=engine)
    yield
```

### Infrastructure Warm-up
Created a Netlify Function that runs every 10 minutes via `netlify.toml` scheduling.
```toml
[[scheduled.functions]]
path = "/.netlify/functions/warmup"
schedule = "*/10 * * * *"
```

### Frontend UX
Enhanced `api/client.js` with a response interceptor that detects cold starts and triggers a retry while updating a global `serverStatus` store.

## Files Created/Modified
- `backend/app/main.py` - Optimized startup and added `/health` endpoint
- `frontend/netlify/functions/warmup.js` [NEW] - Warm-up logic
- `frontend/netlify.toml` [NEW] - Scheduling configuration
- `frontend/src/api/client.js` - Added automatic retry logic
- `frontend/src/stores/serverStatus.js` [NEW] - Managed waking state
- `frontend/src/App.vue` - Added waking notification banner

## Verification
- Confirmed `/health` endpoint is functional.
- Verified Netlify function structure for production.
- Manually tested UI animations and retry behavior during simulated delays.
