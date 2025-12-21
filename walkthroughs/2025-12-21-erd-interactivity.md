# ERD Viewer Interactivity

*Date: 2025-12-21*

## Overview

Added interactive capabilities to the Database Schema Viewer (`SchemaERD.vue`) in the Admin Dashboard. Users can now zoom and pan to explore complex database relationships effectively.

## Implementation Details

### Frontend Components

#### `SchemaERD.vue`
- **Zoom & Pan Engine**: Implemented a manual viewport transformation system using CSS `transform: translate(...) scale(...)`.
- **Mouse Events**:
  - `wheel`: Handles zooming (0.1x to 5x scale).
  - `mousedown`/`mousemove`/`mouseup`: Handles panning (dragging).
- **Controls**: Added a floating toolbar with:
  - **Zoom In (+)**
  - **Zoom Out (-)**
  - **Reset (⟲)**: Restores view to center and 100% scale.
- **Visuals**:
  - `cursor-grab` and `cursor-grabbing` for intuitive drag feedback.
  - Overlay displaying current zoom percentage and pan coordinates.
  - Smooth CSS transitions for zoom actions.

### Technical Challenges
- **Mermaid.js SVG Scaling**: The default mermaid render fits the SVG to the container width, which makes large diagrams unreadable. We disabled `useMaxWidth` in mermaid config and implemented our own viewport container to allow the SVG to retain its natural size and be explored via the window.

## Verification

1.  **Navigate to**: Admin Dashboard -> Database Schema tab.
2.  **Zoom**: Use the scroll wheel or on-screen buttons to zoom in and out.
3.  **Pan**: Click and drag the diagram canvas to move around.
4.  **Reset**: Click the reset button to return to the default view.
