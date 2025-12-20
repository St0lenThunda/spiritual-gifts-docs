# Authentication System Fixes

**Date**: December 16, 2025  
**Status**: Completed ✅

## Summary

Fixed multiple authentication and database connectivity issues that were preventing users from logging in and accessing protected routes.

## Issues Addressed

### 1. Database Connection Pool (Neon Serverless)

**Problem**: `SSL connection has been closed unexpectedly` errors when making database queries.

**Cause**: Neon serverless PostgreSQL closes idle connections, but SQLAlchemy wasn't configured to handle this.

**Solution**: Updated `backend/app/database.py` with connection pool settings:

```python
engine = create_engine(
    DATABASE_URL,
    pool_pre_ping=True,      # Test connections before use
    pool_recycle=300,        # Recycle after 5 minutes
    pool_size=5,
    max_overflow=10
)
```

---

### 2. JWT Token Subject Type

**Problem**: `Subject must be a string` error when validating JWT tokens.

**Cause**: The `jose` library requires the `sub` claim to be a string, but we were storing `user.id` as an integer.

**Solution**: Updated token creation in `dev_auth.py` and `routers.py`:

```diff
- access_token = create_access_token(data={"sub": user.id, "email": user.email})
+ access_token = create_access_token(data={"sub": str(user.id), "email": user.email})
```

Also updated `neon_auth.py` to convert back to int when querying:

```python
user_id = int(payload.get("sub"))
```

---

### 3. Token Storage Timing

**Problem**: `Not authenticated` errors when calling `/auth/me` after login.

**Cause**: Token was being fetched before being saved to localStorage. The API client reads from localStorage for the Authorization header.

**Solution**: Reordered operations in `Login.vue` and `stores/user.js`:

```javascript
// BEFORE: Saved token AFTER fetching user (wrong)
await userStore.fetchCurrentUser();
localStorage.setItem('spiritual_gifts_token', userStore.token);

// AFTER: Save token BEFORE fetching user (correct)
localStorage.setItem('spiritual_gifts_token', userStore.token);
await userStore.fetchCurrentUser();
```

---

### 4. Missing Database Column

**Problem**: `column surveys.user_id does not exist` error on `/user/surveys` endpoint.

**Cause**: The `user_id` column was defined in the SQLAlchemy model but hadn't been migrated to the database.

**Solution**: Ran migration to add the column:

```sql
ALTER TABLE surveys ADD COLUMN user_id INTEGER REFERENCES users(id);
```

---

### 5. Error Display

**Problem**: Axios config object being displayed instead of actual error messages.

**Cause**: Error handlers were logging `err` directly instead of extracting the message.

**Solution**: Updated error handlers to use:

```javascript
err.response?.data?.detail || err.message || 'Default error message'
```

## Files Modified

- `backend/app/database.py` - Connection pool settings
- `backend/app/dev_auth.py` - String conversion for JWT sub
- `backend/app/routers.py` - String conversion for JWT sub
- `backend/app/neon_auth.py` - Int conversion when reading JWT
- `backend/migrate_db.py` - Added user_id migration
- `frontend/src/pages/Login.vue` - Token storage order, error handling
- `frontend/src/stores/user.js` - Token storage order, error handling

## Verification

```bash
# Test login and get token
TOKEN=$(curl -s -X POST http://localhost:8000/auth/dev-login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com"}' | jq -r '.access_token')

# Test authenticated endpoint
curl http://localhost:8000/auth/me -H "Authorization: Bearer $TOKEN"
# Returns: {"id":2,"email":"test@example.com",...}

curl http://localhost:8000/user/surveys -H "Authorization: Bearer $TOKEN"
# Returns: [] (empty array for new user)
```
