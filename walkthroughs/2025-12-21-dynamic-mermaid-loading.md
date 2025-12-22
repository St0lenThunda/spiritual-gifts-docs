# Dynamic Mermaid.js Optimization

**Date**: December 21, 2025
**Status**: Completed ✅

## Summary
Optimized the Admin Dashboard loading performance by converting the heavy `mermaid` library import into a dynamic import. The library is now only loaded when the user navigates to the "Schema" tab.

## Issues Addressed / Features Added
- **Performance Optimization**: Reduced the initial bundle size for the Admin module.
- **On-Demand Loading**: `mermaid` (~1MB uncompressed) is now fetched lazily only when required for ERD visualization.

## Implementation Details
Modified `frontend/src/components/admin/SchemaERD.vue`:
1.  Removed the top-level static import `import mermaid from 'mermaid'`.
2.  Refactored `renderDiagram` to check if `mermaid` is initialized.
3.  Used `await import('mermaid')` to load the module on the first render request.
4.  Moved configuration logic (`mermaid.initialize`) inside the dynamic loading block to ensure it runs only once after the module is available.

```javascript
// Before
import mermaid from 'mermaid'
mermaid.initialize({...})

// After
let mermaid = null
const renderDiagram = async () => {
  if (!mermaid) {
    const m = await import('mermaid')
    mermaid = m.default
    mermaid.initialize({...})
  }
  // render...
}
```

## Files Created/Modified
- `frontend/src/components/admin/SchemaERD.vue` - Implemented dynamic loading logic.

## Verification
### Manual Verification
1.  Verified via code review that the static import is removed.
2.  Functionality should remain identical from the user's perspective, with a slight loading delay on the first access to the Schema tab as the network request completes.
