# SaaS Implementation Plan
## Spiritual Gifts Assessment - Multi-Tenant SaaS Roadmap

*Created: December 24, 2025*  
*Target Launch: Q1 2025*

---

## Executive Summary

This plan outlines the transformation of the Spiritual Gifts Assessment from a single-tenant application to a production-ready SaaS product with multi-tenancy, Stripe billing, and scalable infrastructure.

---

## Phase 1: Foundation (Weeks 1-4)
### Goal: Multi-Tenant Architecture & Deployment

#### 1.1 Multi-Tenancy Implementation

**Database Changes**:
```sql
-- Add organizations table
CREATE TABLE organizations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,  -- subdomain
    plan VARCHAR(50) DEFAULT 'free',
    stripe_customer_id VARCHAR(255),
    created_at TIMESTAMP DEFAULT NOW()
);

-- Add org_id to existing tables
ALTER TABLE users ADD COLUMN org_id UUID REFERENCES organizations(id);
ALTER TABLE surveys ADD COLUMN org_id UUID REFERENCES organizations(id);
ALTER TABLE log_events ADD COLUMN org_id UUID REFERENCES organizations(id);

-- Create indexes for tenant queries
CREATE INDEX idx_users_org ON users(org_id);
CREATE INDEX idx_surveys_org ON surveys(org_id);
```

**Backend Changes**:
- [ ] Create `Organization` model and schema
- [ ] Add `org_id` to all models (User, Survey, LogEvent)
- [ ] Create `get_current_org()` dependency
- [ ] Filter all queries by `org_id`
- [ ] Add organization CRUD endpoints

**Frontend Changes**:
- [ ] Add organization context to Pinia store
- [ ] Update API client with org header
- [ ] Create organization switcher (for users in multiple orgs)

#### 1.2 Infrastructure Setup

**Recommended Stack**:
| Component | Service | Cost/Month |
|-----------|---------|------------|
| Backend Hosting | Railway or Render | $20-50 |
| Database | Neon (current) or Supabase | $0-25 |
| Frontend Hosting | Netlify (current) | $0-19 |
| Domain + SSL | Cloudflare | $0 |
| Email | Resend or SendGrid | $0-20 |
| Monitoring | Sentry + Uptime Robot | $0-29 |

**Total Estimated**: $20-143/month

**Deployment Tasks**:
- [ ] Set up Railway/Render project for backend
- [ ] Configure environment variables securely
- [ ] Set up CI/CD pipeline (GitHub Actions)
- [ ] Configure custom domain with wildcard SSL
- [ ] Set up monitoring and alerting

#### 1.3 Subdomain Routing

**Pattern**: `{org-slug}.spiritualgifts.app`

**Implementation**:
```python
# Backend middleware to extract org from subdomain
@app.middleware("http")
async def org_middleware(request: Request, call_next):
    host = request.headers.get("host", "")
    subdomain = host.split(".")[0]
    
    if subdomain not in ["www", "api", "app"]:
        org = await get_org_by_slug(subdomain)
        request.state.org = org
    
    return await call_next(request)
```

---

## Phase 2: Monetization (Weeks 5-8)
### Goal: Stripe Integration & Pricing Tiers

#### 2.1 Pricing Structure

| Plan | Price | Users | Features |
|------|-------|-------|----------|
| **Free** | $0/month | 10 | Basic assessment, no exports |
| **Starter** | $29/month | 50 | PDF exports, email results |
| **Growth** | $79/month | 200 | Admin dashboard, analytics |
| **Enterprise** | $199/month | Unlimited | Custom branding, API access, SSO |

#### 2.2 Stripe Integration

**Setup Tasks**:
- [ ] Create Stripe account and products
- [ ] Install `stripe` Python package
- [ ] Create subscription endpoints:
  - `POST /api/v1/billing/create-checkout-session`
  - `POST /api/v1/billing/webhook`
  - `GET /api/v1/billing/portal`
- [ ] Implement webhook handlers for:
  - `checkout.session.completed`
  - `customer.subscription.updated`
  - `customer.subscription.deleted`
  - `invoice.payment_failed`

**Billing Endpoints**:
```python
@router.post("/billing/create-checkout-session")
async def create_checkout_session(
    plan: str,
    org: Organization = Depends(get_current_org)
):
    session = stripe.checkout.Session.create(
        customer=org.stripe_customer_id,
        payment_method_types=["card"],
        line_items=[{"price": PRICE_IDS[plan], "quantity": 1}],
        mode="subscription",
        success_url=f"{FRONTEND_URL}/billing/success",
        cancel_url=f"{FRONTEND_URL}/billing/cancel",
    )
    return {"url": session.url}
```

#### 2.3 Plan Enforcement

**Feature Gating**:
```python
def check_plan_limit(org: Organization, feature: str):
    limits = {
        "free": {"users": 10, "exports": False},
        "starter": {"users": 50, "exports": True},
        "growth": {"users": 200, "exports": True, "admin": True},
        "enterprise": {"users": float("inf"), "exports": True, "admin": True, "api": True},
    }
    return limits.get(org.plan, limits["free"])
```

---

## Phase 3: Onboarding & Growth (Weeks 9-12)
### Goal: User Acquisition & Retention

#### 3.1 Onboarding Flow

**New Organization Setup Wizard**:
1. Create account (magic link)
2. Name your organization
3. Choose subdomain (grace.spiritualgifts.app)
4. Invite team members
5. Start first assessment

**Implementation**:
- [ ] Create `/onboarding` page with step wizard
- [ ] Add organization creation API
- [ ] Implement invite email system
- [ ] Create welcome email sequence (3-5 emails)

#### 3.2 Marketing Website

**Landing Page Sections**:
- Hero with value proposition
- Feature overview with screenshots
- Pricing table
- Testimonials (gather from beta users)
- FAQ
- CTA: "Start Free Trial"

**SEO Strategy**:
- Target keywords: "spiritual gifts test", "spiritual gifts assessment", "church volunteer placement"
- Create blog content about spiritual gifts
- Add schema.org markup for rich results

#### 3.3 Analytics & Metrics

**Key Metrics to Track**:
| Metric | Target | Tool |
|--------|--------|------|
| Monthly Active Orgs | 50+ | PostHog |
| Trial → Paid Conversion | 10%+ | Stripe Dashboard |
| Churn Rate | <5% | Stripe Dashboard |
| Assessments/Week | 500+ | Internal Analytics |
| NPS Score | 40+ | Survey |

---

## Phase 4: Scale & Optimize (Weeks 13+)
### Goal: Enterprise Features & Market Expansion

#### 4.1 Enterprise Features

- [ ] **Custom Branding**: Logo, colors, custom domain
- [ ] **SSO Integration**: SAML, OAuth (Google, Microsoft)
- [ ] **API Access**: REST API for integration with church systems
- [ ] **Advanced Analytics**: Congregation-wide insights, cohort analysis
- [ ] **Data Export**: CSV/Excel exports for ministry planning

#### 4.2 Scalability Improvements

- [ ] Add Redis caching for session and rate limiting
- [ ] Implement database connection pooling
- [ ] Add CDN for static assets
- [ ] Consider read replicas if needed

#### 4.3 Compliance & Trust

- [ ] Create Privacy Policy and Terms of Service
- [ ] Add GDPR data export/deletion features
- [ ] Consider SOC 2 Type 1 compliance
- [ ] Create security documentation

---

## Technical Checklist Summary

### Backend
- [ ] Organization model + migrations
- [ ] Tenant isolation on all queries
- [ ] Stripe billing integration
- [ ] Subdomain routing middleware
- [ ] Plan enforcement decorators
- [ ] Invite system with email

### Frontend
- [ ] Organization context in store
- [ ] Onboarding wizard
- [ ] Billing/subscription UI
- [ ] Organization settings page
- [ ] User management (invite, roles)

### Infrastructure
- [ ] Production hosting (Railway/Render)
- [ ] Wildcard SSL certificate
- [ ] CI/CD pipeline
- [ ] Monitoring + alerting
- [ ] Backup strategy

### Business
- [ ] Stripe account setup
- [ ] Privacy policy & ToS
- [ ] Support email/system
- [ ] Documentation/help center
- [ ] Marketing website

---

## Budget Estimate

### One-Time Costs
| Item | Cost |
|------|------|
| Domain (spiritualgifts.app) | $15/year |
| Legal (Privacy Policy, ToS) | $0-500 |
| Stripe setup | $0 |
| **Total** | ~$15-515 |

### Monthly Operating Costs
| Item | Free Tier | With Customers |
|------|-----------|----------------|
| Hosting (Backend) | $0-20 | $50-100 |
| Database | $0 | $25-50 |
| Frontend (Netlify) | $0 | $19 |
| Email (Resend) | $0 | $20 |
| Monitoring | $0 | $29 |
| **Total** | $0-20 | $143-218 |

### Break-Even Analysis
At $29/month starter plan:
- Break-even at ~5-8 paying customers
- At 50 customers: ~$1,450/month revenue
- At 100 customers: ~$2,900/month revenue

---

## Timeline

```
Week 1-2:  Database schema + Organization model
Week 3-4:  Tenant isolation + Subdomain routing
Week 5-6:  Stripe integration + Pricing tiers
Week 7-8:  Billing UI + Webhook handling
Week 9-10: Onboarding wizard + Invite system
Week 11-12: Marketing site + Beta launch
Week 13+:  Iterate based on feedback
```

---

## Next Immediate Steps

1. **Create Organization model** and database migration
2. **Set up Railway/Render** deployment for backend
3. **Create Stripe account** and configure products
4. **Build onboarding wizard** in frontend
5. **Recruit 3-5 pilot churches** for beta testing

---

## Success Criteria

### 30-Day Goals
- [ ] Multi-tenant architecture deployed
- [ ] 3+ pilot churches onboarded
- [ ] Basic billing functional

### 90-Day Goals
- [ ] 10+ paying customers
- [ ] <5% churn rate
- [ ] 100+ assessments completed

### 1-Year Goals
- [ ] 100+ organizations
- [ ] $5,000+ MRR
- [ ] Enterprise plan launched
