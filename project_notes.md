# Project Status & Roadmap

This document tracks the current status of the Called & Equipped project and outlines future improvements.

## 🚀 Future Improvements

### 🏗️ Architecture
- **[new] Evaluate migration to [Render.com](https://render.com/) for consolidated stack management**.
  - **Current State**: Split deployment between Netlify (Frontend) and Neon/External (Backend/DB).
  - **Reason**: Consolidating on Render allows for private networking (secure DB access without public IPs) and a unified deployment dashboard.
  - **Trade-offs**: Requires moving away from "serverless" free-forever tiers (Neon) to managed instances (~$7/mo for DB and ~$7/mo for API) to avoid cold-starts and data expiration.

### 🧪 Engineering Excellence
- **CI/CD Pipeline**: Integrate GitHub Actions for automated linting and test execution on every PR.
- **Docker Containerization**: Containerize both frontend and backend for guaranteed environment parity.

---

## ✅ Recently Completed
- ✅ **Environment-Aware Dev Mode**: Fixed login screen "dev mode" appearing in production.
- ✅ **E2E Test Stabilization**: Hardened Playwright assessment tests for production readiness.
- ✅ **Theological Profile System**: Multi-denominational support with Model C configuration.
- ✅ **Structured Logging**: `structlog` integration with request correlation.

---

## 📊 Summary Assessment

| Category | Status | Rating |
| :--- | :--- | :--- |
| **Authentication** | Stable | 🟢 Managed |
| **Assessment Logic** | Stable | 🟢 Managed |
| **Data Integrity** | Stable | 🟢 Managed |
| **Observability** | Enhanced | 🟢 Managed |
| **Localization** | Comprehensive | 🟢 Managed |
| **Deployment** | Split | 🟡 Optimized |
