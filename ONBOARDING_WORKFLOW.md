# Internal Onboarding Workflow Reference

**Version**: 1.0 (Internal)
**Last Updated**: 2026-01-01
**Status**: Formalized

## Executive Summary
This document outlines the systematic process by which users enter the "Called & Equipped" ecosystem, join organizations, and upgrade tiers. It serves as the canonical reference for frontend logic and backend state transitions.

---

## 1. User Entry & Initial Onboarding
All newly authenticated users who are not yet affiliated with an organization are met with the **Onboarding Modal**.

### Decision Matrix
| Path | User Action | System Outcome |
| :--- | :--- | :--- |
| **Individual** | Click "Continue as Individual" | Executed `joinNeonEvangelion`. User becomes member of `neon-evangelion`. `standalone_mode` set to `true`. |
| **Organization** | Click "Join or Create" | Redirection to `/settings/organization`. User can search or create. |

> [!NOTE]
> System Admins and users with an existing `org_id` bypass this modal automatically to prevent workflow interruption.

---

## 2. Organization Lifecycle & Tiers
Affiliation status determines the user's dashboard experience and administrative capabilities.

### Tier Levels (Entitlements)
| Tier | Internal Name (Legacy) | Key Features | Caps |
| :--- | :--- | :--- | :--- |
| **Free** | `free` | Basic assessment & history. | 10 Members, 0 Admins |
| **Individual** | `individual` (`starter`) | PDF Exports, Email Support. | 50 Members, 1 Admin |
| **Ministry** | `ministry` (`growth`) | Admin Analytics, Member Management. | 100 Members, 5 Admins |
| **Church** | `church` (`enterprise`) | Custom Branding Designer, SSO, API. | Unlimited |

---

## 3. Member Onboarding Workflow
The process for joining an existing organization follows a request-approval cycle.

### Steps:
1. **Search**: User find org via Name or Slug in the Org Settings page.
2. **Request**: User clicks "Request to Join".
3. **Pending State**: User is assigned `membership_status: 'pending'` and a `user` role.
4. **Notification**: Org Admins receive:
   - A pulsing red dot on their avatar.
   - A "Pending Member Alert" popup in the bottom-right.
5. **Review**: Admin visits the Members tab in settings to Approve/Reject.
6. **Activation**: Upon approval, the user gains access to the organization's shared data and branding.

---

## 4. Administrative Roles
- **System Admin**: Global oversight (all organizations). Bypasses onboarding restrictions.
- **Organization Admin**: Can manage members, billing, and branding within their specific organization.
- **Organization Member**: Can only take assessments and view personal/shared results within the organization context.

---

## 5. Billing & Subscription Management
- **Incomplete Setup**: If an organization is on a paid tier but lacks a `stripe_customer_id`, the system prompts the Admin to complete checkout rather than managing a non-existent portal session.
- **Portal Access**: Active subscriptions use the `create-portal-session` endpoint to redirect to Stripe for card management and cancellations.
- **Checkout**: New upgrades use `create-checkout-session` with plan-specific metadata for automatic activation upon Stripe webhook callback.

---

### Related Documentation
- [Tier Feature Matrix](file:///home/thunda/Dev/543_Tools/spiritual-gifts/docs/public/06%20-%20Pricing%20%26%20Monetization/Tier%20Feature%20Matrix.md)
- [Stripe Integration Guide](file:///home/thunda/Dev/543_Tools/spiritual_gifts_backend/app/routers/billing.py)
