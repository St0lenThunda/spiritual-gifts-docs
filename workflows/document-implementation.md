---
description: How to document completed implementations
---

# Post-Implementation Documentation Workflow

After completing any significant implementation work on this project, follow these steps:

## 1. Create Walkthrough Document

Create a new walkthrough document in `/home/thunda/Dev/543_Tools/spiritual-gifts/docs/walkthroughs/` using the naming convention:

```
YYYY-MM-DD-brief-description.md
```

Example: `2025-12-17-user-profile-feature.md`

## 2. Walkthrough Template

Include these sections:

```markdown
# [Feature/Fix Name]

**Date**: [Month Day, Year]
**Status**: Completed ✅

## Summary
Brief description of what was implemented

## Issues Addressed / Features Added
- Point 1
- Point 2

## Implementation Details
Technical details, code examples, architecture decisions.

## Files Created/Modified
- `path/to/file1.js` - Description
- `path/to/file2.py` - Description

## Verification
How the implementation was tested/validated.
```

## 3. Update README Index

Add a new row to the index table in `/home/thunda/Dev/543_Tools/spiritual-gifts/docs/walkthroughs/README.md`:

```markdown
| YYYY-MM-DD | [Title](./YYYY-MM-DD-description.md) | Brief description |
```

Keep entries in reverse chronological order (newest first).

## 4. Other Documentation to Consider

Based on the type of change, also update:

| Change Type | Documentation to Update |
|-------------|------------------------|
| New API endpoint | `docs/api/` (create if needed) |
| New component | Component JSDoc comments |
| Database changes | `docs/database/` schema docs |
| Configuration | `.env.example`, `README.md` |
| New feature | User-facing docs if applicable |
