# PII Redaction in Logs

**Date**: December 21, 2025
**Status**: Completed ✅

## Summary
Implemented a privacy protection layer in the logging system to redaction Personally Identifiable Information (PII). Specifically, user email addresses are now masked (e.g., `j***@example.com`) before being securely logged to the database or console.

## Issues Addressed / Features Added
- **Privacy Compliance**: Reduces risk of leaking sensitive user data in logs.
- **Security Best Practice**: Ensures PII is minimized in non-production data stores (like log tables).

## Implementation Details
Modified `app/logging_setup.py`:
1.  **`mask_email` Function**: A helper utility that takes an email string and returns a masked version.
    - `johndoe@example.com` -> `j***@example.com`
    - `a@b.com` -> `***@b.com`
2.  **`pii_masking_processor`**: A `structlog` processor inserted into the processing chain immediately after `merge_contextvars`. It inspects the `event_dict` for a `user_email` key and applies the mask if found.
3.  **Processor Chain Update**: The new processor ensures that subsequent processors (like the database logger or JSON renderer) only receive the sanitized email.

```python
# app/logging_setup.py

def mask_email(email: str) -> str:
    # ... implementation ...

def pii_masking_processor(logger, method_name, event_dict):
    if "user_email" in event_dict:
        event_dict["user_email"] = mask_email(event_dict["user_email"])
    return event_dict

# In setup_logging():
processors = [
    structlog.contextvars.merge_contextvars,
    pii_masking_processor,  # <-- Added here
    # ... other processors
]
```

## Files Created/Modified
- `app/logging_setup.py` - Added masking logic and processor.
- `tests/test_pii_masking.py` - Added verification tests.

## Verification
### Automated Tests
- `pytest tests/test_pii_masking.py`: Verifies that emails are correctly masked in various scenarios (standard, short, invalid) and that the processor correctly modifies the event dictionary.
