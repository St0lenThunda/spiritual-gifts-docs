# Developer Logins & Scenarios

These logins are automatically seeded when running `scripts/setup_demo_env.py` (which runs on `start_dev.sh`).

## 🔑 How to Login
1. Navigate to `/login`.
2. Enter the email address below.
3. Click "Sign with Email".
4. **Dev Mode**: You will be instantly logged in (Password/Magic Link bypassed).

## 🧪 Scenarios

### 1. Super Admin (Owner)
**Email:** `tonym415@gmail.com`
- **Role**: Admin
- **Org**: Neon Evangelion Ministry (Growth/Individual Plan)
- **Privileges**:
    - Full access to Tenant Dashboard (`/dashboard`)
    - **EXCLUSIVE** access to Global System Admin (`/admin/logs`, `/admin/users`)

### 2. Tiered Test Scenarios

The following users are explicitly seeded to test specific subscription tiers and organizational contexts.

| Tier | Organization | Admin User (Login) | Member User (Login) |
| :--- | :--- | :--- | :--- |
| **Individual** | Neon Evangelion* | `tonym415@gmail.com` | *(N/A - Single User)* |
| **Ministry** | Grace Community | `david.chen@gracecommunity.org` | `marcus.j@gracecommunity.org` |
| **Church** | Harvest Point | `angela.w@harvestpoint.church` | `daniel.o@harvestpoint.church` |

> [!NOTE]
> **Demo Functionality**: The **Grace Community Fellowship** organization is configured as the **Read-Only Demo**. Use `david.chen@gracecommunity.org` to test the "Demo Mode" restrictions (e.g., inability to change settings or edit data).

* *Note: "Neon Evangelion" is on the `growth` plan, which typically maps to the Individual/Pro feature set.*

### 3. New User (Onboarding)
**Email:** `[any_new_email]@example.com`
- **Experience**:
    - Auto-assigned to **Grace Community Fellowship** (Demo Mode).
    - Can explore assessments immediately.
    - Read-Only access if attempting to change Org settings.

