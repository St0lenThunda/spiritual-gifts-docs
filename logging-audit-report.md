# Logging Audit & Expansion Strategy

This document summarizes the current state of structured logging in the Spiritual Gifts Assessment backend and proposes a roadmap for enhanced observability.

---

## 📋 Current Log Events
The following events are currently recorded in the `log_entries` database table.

### Authentication Flow
| Event | Level | Description | Captured Context |
| :--- | :--- | :--- | :--- |
| `magic_link_sent` | `INFO` | Email dispatch request successful. | `user_email` |
| `magic_link_send_failed` | `ERROR` | Failed to dispatch magic link. | `user_email`, `error` |
| `magic_link_verified` | `INFO` | Successful login via magic link. | `user_id`, `user_email` |
| `dev_login_successful` | `INFO` | Development bypass login used. | `user_email` |
| `user_logged_out` | `INFO` | Session terminated by user. | `user_id`, `user_email` |

### Survey (Assessment) Flow
| Event | Level | Description | Captured Context |
| :--- | :--- | :--- | :--- |
| `survey_submitted` | `INFO` | New assessment successfully stored. | `user_id`, `survey_id` |

### Global System Events
| Event | Level | Description | Captured Context |
| :--- | :--- | :--- | :--- |
| `request_completed` | `INFO` | Telemetry for every API call. | `path`, `method`, `status_code`, `duration` |
| `unhandled_exception` | `ERROR` | Application crash or 500 error. | `exception` (traceback), `path`, `method`, `user_id` |

---

## 🚀 Suggested Enhancements

### 1. Security & Rate Limiting (Audit Logs)
- **`rate_limit_exceeded`**: Log when a user hits a 429. This helps identify bot activity or users struggling with login.
- **`unauthorized_access`**: Explicitly log 401/403 errors when protected routes are accessed with invalid/missing cookies.
- **`invalid_session_token`**: Log the *reason* for session failure (e.g., "token expired" vs "signature mismatch").

### 2. Performance & Service Monitoring
- **`db_query_slow`**: Log any database operation that takes longer than a defined threshold (e.g., 500ms).
- **`neon_api_latency`**: Track how long the external Neon Auth API takes to respond, as this affects login UX.
- **`resource_load_failure`**: Log if system files like `questions.json` or `gifts.json` fail to parse at startup.

### 3. Business & Usage Analytics
- **`new_user_created`**: Track when a unique email registers for the first time.
- **`survey_history_viewed`**: Track when users access their previous results to understand engagement.
- **`gift_definitions_accessed`**: Track which spiritual gifts users are most interested in researching.

### 4. Debugging & Development
- **`config_initialized`**: Log configuration settings (excluding secrets) on startup for environment verification.
- **`middleware_skipped`**: Log if specific paths (like `/health`) are bypassed for any reason.

---

## 🛠 Next Steps
1. **Audit Review**: Confirm the current event list meets auditing requirements.
2. **Prioritize Suggestions**: Select 2-3 "High Value" suggestions for immediate implementation (e.g., Rate Limiting and New User tracking).
3. **Frontend Correlation**: Consider passing a `X-Request-ID` from the frontend to correlate browser-side errors with backend database logs.
