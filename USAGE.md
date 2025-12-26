# Developer Logins & Scenarios

These logins are automatically seeded when running `scripts/setup_demo_env.py` (which runs on `start_dev.sh`).

## 🔑 How to Login
1. Navigate to `/login`.
2. Enter the email address below.
3. Click "Sign with Email".
4. **Dev Mode**: You will be instantly logged in (Password/Magic Link bypassed).

## 🧪 Scenarios

### 1. Super Admin (Owner)
**Email:** `tonym415@gmail.com` OR `super@neon.com`
- **Role**: Admin
- **Org**: Neon Evangelion Ministry
- **Privileges**:
    - Full access to Tenant Dashboard (`/dashboard`)
    - **EXCLUSIVE** access to Global System Admin (`/admin/logs`, `/admin/users`)
    - Can view cross-org analytics.

### 2. Standard Org Admin (Customer)
**Email:** `admin@grace.com`
- **Role**: Admin
- **Org**: Grace Community Fellowship (Demo Org)
- **Privileges**:
    - Full access to Organization Settings & Analytics.
    - Can manage members *within* Grace Community.
    - **BLOCKED** from Global `/admin` routes.

### 3. Organization Member
**Email:** `user@grace.com`
- **Role**: User
- **Org**: Grace Community Fellowship (Demo Org)
- **Privileges**:
    - Can take assessments.
    - Can view their own history.
    - **BLOCKED** from Org Settings & Analytics.

### 4. New User (Onboarding)
**Email:** `[any_new_email]@example.com`
- **Experience**:
    - Auto-assigned to **Grace Community Fellowship** (Demo Mode).
    - Can explore assessments immediately.
    - Read-Only access if attempting to change Org settings.
