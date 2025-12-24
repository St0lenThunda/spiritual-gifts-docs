# Billing API

Endpoints for managing subscriptions and payments via Stripe.

## Endpoints

### `POST /api/v1/billing/create-checkout-session`
Create a Stripe checkout session for a specific plan.

**Parameters:**
- `plan` (query): One of `starter`, `growth`, `enterprise`.
- `success_url` (query, optional): Redirect URL after successful payment.
- `cancel_url` (query, optional): Redirect URL after canceled payment.

**Response:**
```json
{
  "url": "https://checkout.stripe.com/..."
}
```

---

### `POST /api/v1/billing/create-portal-session`
Create a Stripe Customer Portal session for subscription management (upgrade, downgrade, cancel, payment methods).

**Parameters:**
- `return_url` (query, optional): URL to return to after leaving the portal.

**Response:**
```json
{
  "url": "https://billing.stripe.com/..."
}
```

---

### `GET /api/v1/billing/status`
Get the current billing and subscription status for the organization.

**Response:**
```json
{
  "plan": "starter",
  "subscription_status": "active",
  "current_period_end": "2025-01-24T12:00:00",
  "limits": {
    "users": 50,
    "exports": true,
    "admin": false,
    "api": false
  }
}
```

---

### `POST /api/v1/billing/webhook`
Public endpoint for Stripe webhook events. Requires valid `stripe-signature` header.

**Events Handled:**
- `checkout.session.completed`: Upgrades org plan and stores subscription ID.
- `customer.subscription.updated`: Syncs status and period end date.
- `customer.subscription.deleted`: Reverts org to `free` plan.
