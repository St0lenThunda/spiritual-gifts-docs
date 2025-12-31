# ADR-030: Theological Profile Configuration

| Status | Proposed |
|:---:|:---:|
| **Date** | 2025-12-31 |
| **Decision Owner** | Thunda |
| **Teams Involved** | Engineering, Product, Theology |

## Context

The platform currently operates with a single, implicit theological perspective (generic/evangelical Protestant). To support a wider range of Christian traditions (e.g., Catholic, Mainline, Charismatic) without fracturing the core user experience or maintaining separate codebases, we need a configurable architecture.

The goal is to allow organizations to select a "Theological Profile" (Denomination) that adjusts terminology, scripture references, and interpretive framing while maintaining the core assessment mechanics and data ownership.

## Decision

We will implement a **"Shared Core, Configured Interpretations"** model (Model C).

1.  **Denomination Entity**: A new `Denomination` model will store configuration bundles (slug, display name, default currency, preferred terminology).
2.  **Scripture Sets**: A `ScriptureSet` model will allow denominations to prefer specific translations or verses for key concepts.
3.  **Organization Configuration**: Organizations will have a `denomination_id` foreign key.
4.  **Frontend Context**: The frontend will use a reactive store to provide the current denomination's configuration to components, enabling dynamic text replacement (i18n namespaces) and logic adjustments.
5.  **Invariant Core**: Assessment algorithms, privacy rules, and admin boundaries remain invariant across all denominations.

## Rationale

*   **Maintainability**: A shared codebase avoids the "forking hell" of maintaining separate apps for each tradition.
*   **Scalability**: New denominations can be added as data/configuration rather than code changes.
*   **Trust**: Explicitly modeling theological differences builds trust with diverse user bases by speaking their language.
*   **Reversibility**: Denominational selection is non-destructive; an organization can switch profiles without losing data.

## Consequences

*   **Complexity**: Adds a layer of indirection for UI text and scripture references. Developers must check the active denomination context.
*   **Data Migration**: Existing organizations need to be backfilled with a default denomination (done: "Spiritual Gifts" generic profile).
*   **Testing**: E2E testing must cover multiple denominational configurations to ensure UI adaptability.

## Implementation Details

*   **Backend**: FastAPI service with `Denomination` and `ScriptureSet` tables.
*   **Frontend**: `DenominationStore` (Pinia) and `DenominationSelector.vue` component.
*   **i18n**: New `denomination` namespace in locale files for dynamic terminology.
