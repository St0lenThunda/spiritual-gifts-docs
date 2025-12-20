# Codebase Documentation

**Date**: December 12, 2025  
**Status**: Completed ✅

## Summary

Added comprehensive comments and docstrings throughout the entire codebase to improve maintainability and developer onboarding.

## Documentation Added

### Frontend (Vue Components)

- **File-level comments** at the top of each `.vue` file describing the component's purpose
- **JSDoc-style comments** for:
  - Props definitions with types and descriptions
  - Emitted events
  - Computed properties
  - Methods/functions
- **Inline comments** for complex template logic and non-obvious code paths

### Backend (Python Modules)

- **Module-level docstrings** explaining the purpose of each file
- **Function docstrings** with:
  - Description of what the function does
  - `Args:` section documenting parameters
  - `Returns:` section documenting return values
  - `Raises:` section for exceptions (where applicable)
- **Class docstrings** for models and schemas
- **Inline comments** for complex logic

## Example: Vue Component Documentation

```vue
/**
 * ScripturePopover.vue
 * 
 * A popover component that displays scripture references with multiple
 * translation options. Uses Floating UI for positioning.
 */
<script setup>
/**
 * Props for the ScripturePopover component.
 * @prop {string} reference - The scripture reference (e.g., "Romans 12:6-8")
 * @prop {boolean} inline - Whether to render inline or as a block element
 */
defineProps({
  reference: { type: String, required: true },
  inline: { type: Boolean, default: true }
})

/**
 * Emits when the popover is opened or closed.
 * @event toggle
 * @param {boolean} isOpen - Whether the popover is now open
 */
defineEmits(['toggle'])
</script>
```

## Example: Python Function Documentation

```python
async def get_current_user(
    credentials: HTTPAuthorizationCredentials = Depends(security),
    db: Session = Depends(get_db)
) -> User:
    """
    Get the current authenticated user from JWT token.
    
    Args:
        credentials: HTTP Bearer credentials from request header
        db: Database session
        
    Returns:
        User object for the authenticated user
        
    Raises:
        HTTPException: If authentication fails (401 Unauthorized)
    """
```

## Files Documented

### Frontend
- All components in `src/components/`
- All pages in `src/pages/`
- Store modules in `src/stores/`
- API clients in `src/api/`
- Router configuration
- Main application entry point

### Backend
- `main.py` - FastAPI application setup
- `routers.py` - API route handlers
- `models.py` - SQLAlchemy models
- `schemas.py` - Pydantic schemas
- `database.py` - Database configuration
- `neon_auth.py` - Authentication utilities
- `dev_auth.py` - Development authentication
- `config.py` - Settings management
