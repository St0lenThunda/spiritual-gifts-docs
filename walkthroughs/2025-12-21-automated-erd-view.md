# Automated Database ERD: In-Dashboard Visualization

**Date**: December 21, 2024
**Status**: Completed ✅

## Summary
Implemented an automated Entity Relationship Diagram (ERD) view within the Admin Dashboard. This feature programmatically inspects the SQLAlchemy models and generates a live visualization of the database schema using Mermaid.js, ensuring that technical documentation is always in sync with the codebase.

## Issues Addressed
- **Stale Documentation**: Database schemas are often documented manually and become outdated as the models evolve.
- **Architectural Oversight**: Developers and admins need a quick way to verify relationships (FKs) and column types without accessing raw SQL tools.

## Implementation Details

### 1. Dynamic Backend Generation
Added a new `/admin/schema` endpoint in `spiritual_gifts_backend/app/routers/admin.py` that iterates through the SQLAlchemy `Base.metadata.tables`. It translates Python/SQL types into human-readable labels and maps foreign key relationships into Mermaid's `erDiagram` syntax.

### 2. Mermaid.js Integration
Installed the `mermaid` npm package and created a dedicated `SchemaERD.vue` component. This component:
- Fetches the Mermaid syntax from the backend.
- Renders a responsive, high-fidelity SVG diagram.
- Applies custom CSS overrides to match the platform's **Synth-Aesthetic** (neon cyan nodes, neon pink relationship lines).

### 3. Admin UI Integration
Enabled a new **"Database Schema"** tab in the `AdminDashboard.vue`, allowing for seamless switching between logs, user management, and architectural oversight.

## Files Created/Modified
- `spiritual_gifts_backend/app/routers/admin.py`: Added `/schema` generation logic.
- `frontend/src/components/admin/SchemaERD.vue`: [NEW] The visualization engine.
- `frontend/src/pages/AdminDashboard.vue`: Integrated the new tab and component.
- `spiritual_gifts_backend/tests/test_admin.py`: Added automated verification tests for the schema endpoint.

## Verification
- **Automated Tests**: New tests ensure the endpoint returns valid `erDiagram` syntax and includes core tables (`users`, `surveys`, `log_entries`).
- **Visual Accuracy**: Verified that relationships like `users ||--o{ surveys` are correctly represented with cardinality and descriptions.
- **Responsiveness**: The diagram scales automatically to fit the dashboard width.
