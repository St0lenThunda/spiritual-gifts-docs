# Structured Logging & Backend Observability

**Date**: December 21, 2025
**Status**: Completed ✅

## Summary
Implemented structured logging using `structlog` to improve backend observability. All log events and unhandled exceptions are now recorded in a dedicated Neon database table (`log_entries`), including request context (path, method, status code) and authenticated user identity.

## Features Added
- **Database Log Sink**: A new `log_entries` table in PostgreSQL captures every significant backend event.
- **Request Middleware**: Automatically logs every request's completion with its duration and status code.
- **Enhanced Logout Tracking**: Logout events now capture the `user_id` and `user_email` by identifying the user before clearing the session cookie.
- **Dev-Login Monitoring**: Use of the development login bypass is now explicitly logged with the target user's email.
- **Exception Tracking**: Unhandled exceptions are caught, logged with full tracebacks, and associated with the current user.
- **Logging Audit**: A comprehensive report of all current events and future suggestions is available in [logging-audit-report.md](../logging-audit-report.md).
- **Contextual Awareness**: Logging automatically captures `user_id` and `user_email` for authenticated requests without manual intervention in every log call.

## Implementation Details

### Database Schema
A new table `log_entries` was created via Alembic migrations.

```python
class LogEntry(Base):
    id = Column(Integer, primary_key=True)
    timestamp = Column(DateTime, default=datetime.utcnow, index=True)
    level = Column(String, index=True)
    event = Column(String, index=True)
    user_email = Column(String, index=True)
    path = Column(String, index=True)
    context = Column(JSON) # Stores additional structured data
    exception = Column(String) # Stores tracebacks
```

### Structlog Configuration
Configured with context variable merging and a custom database processor.

```python
# logging_setup.py
def db_logger_processor(logger, method_name, event_dict):
    db = database.SessionLocal()
    # Writes event_dict fields to LogEntry model
    # ...
    return event_dict
```

### Middleware Integration
Captured request lifecycle in `main.py`.

```python
@app.middleware("http")
async def logging_middleware(request, call_next):
    path_ctx.set(request.url.path)
    try:
        response = await call_next(request)
        logger.info("request_completed", status_code=response.status_code)
        return response
    except Exception as e:
        logger.error("unhandled_exception", exception=traceback.format_exc())
        raise
```

## Verification Results
### Automated Tests
- `tests/test_logging.py` verified that:
    - Path and method are captured for public routes.
    - User email and identity are captured for authenticated routes.
    - Unhandled exceptions are correctly written to the database.

## Files Created/Modified
- `app/logging_setup.py` [NEW]
- `app/models.py` - Added `LogEntry` model.
- `app/main.py` - Integrated logging middleware.
- `app/neon_auth.py` - Added logging context binding.
- `app/routers.py` - Added semantic logs for auth and survey flows.
- `requirements.txt` - Added `structlog`.
- `alembic/versions/..._add_log_entries_table.py` [NEW]
- `tests/test_logging.py` [NEW]
