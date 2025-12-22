# Request-ID Correlation for Error Tracking

**Date**: December 21, 2025
**Status**: Completed ✅

## Summary
Implemented a full-stack `X-Request-ID` correlation system. This feature generates a unique ID for every API request on the frontend, propagates it to the backend via headers, ensures it is included in all backend logs, and displays it to the user in the UI when an error occurs. This allows for rapid debugging of user-reported issues.

## Issues Addressed / Features Added
- **Frontend Error Reporting**: Users can now provide a specific "Request ID" when reporting errors, eliminating ambiguity.
- **Log Correlation**: Developers can filter logs by `request_id` to trace a single user action across the entire backend stack (middleware, routers, services, DB).
- **Support Efficiency**: Reduced time-to-resolution for support tickets by pinpointing exact error traces immediately.

## Implementation Details

### Frontend Client
The Axios client in `src/api/client.js` was updated with:
1.  **Request Interceptor**: Generates a UUID (using `self.crypto.randomUUID` or a fallback) and attaches it to the `X-Request-ID` header of every outgoing request.
2.  **Response Interceptor**: In case of errors (especially 500s), extracts the Request ID from the response headers or body and displays it in the Toast notification.

### Backend Middleware
The FastAPI application in `app/main.py` was updated with:
1.  **`logging_middleware`**: Reads the `X-Request-ID` header (or generates one if missing).
2.  **Context Variables**: Sets the Request ID in a context variable for global access.
3.  **Structlog Binding**: Binds the Request ID to the logger context, ensuring ALL subsequent logs for that request include the ID automatically.
4.  **Exception Handling**: Catches unhandled exceptions, logs them with the ID, and returns a JSON response containing the `request_id` so the frontend can display it.

## Files Created/Modified
- `frontend/src/api/client.js` - Added interceptors for ID generation and error display.
- `app/main.py` - Added middleware for ID propagation and structlog binding.

## Verification
### Automated Tests
- `tests/test_request_id_propagation.py`: Verifies that the Request ID sent by the client is correctly returned in headers and error bodies.

### Manual Verification
1.  Triggered a 500 error (via a test endpoint or dev tool).
2.  Verified the "Request ID: <uuid>" appeared in the error toast on the frontend.
3.  Checked the backend logs and confirmed the same UUID was present in the error log entry.
