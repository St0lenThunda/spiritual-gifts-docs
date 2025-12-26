# Branding Update: Called & Equipped

**Date**: 2025-12-25
**Status**: Completed ✅

## Summary
Executed a comprehensive rebranding of the platform from "Spiritual Gifts Assessment" to **"Called & Equipped"**. This shift frames the tool as a formation and discernment platform rather than just a testing tool, aligning with the strategic "Discernment Process" language.

## Changes Implemented

### 1. Brand Identity
- **Product Name**: Changed to "Called & Equipped".
- **Subtitle**: "A discernment and formation platform for faithful service."
- **Meta Description**: Updated to reflect the formation-focused value proposition.

### 2. Language Shift (UI Text)
Updated all English locale strings (`en.json`) to remove "test" language and adopt "discernment" language:

| Previous Term | New Term |
|---|---|
| Assessment | **Discernment Process** |
| Results | **Discernment Profile** |
| Admin Dashboard | **Leadership Overview** |
| User Account | **Ministry Profile** |
| Scoring | **Discernment Indicators** |

### 3. Tier Renaming
Updated the SaaS tier display names to better resonate with church audiences:

| Technical ID (Unchanged) | Old Display Name | **New Display Name** |
|---|---|---|
| `starter` | Starter | **Individual** |
| `growth` | Growth | **Ministry** |
| `enterprise` | Enterprise | **Church / Organization** |

*Note: Backend plan slugs (`starter`, `growth`, `enterprise`) remain unchanged to ensure stability.*

## Files Modified
- `frontend/src/locales/en.json` - Comprehensive text updates.
- `frontend/index.html` - Title and meta tags.
- `frontend/src/pages/OrganizationSettings.vue` - Tier display logic.
- `frontend/src/pages/__tests__/OrganizationSettings.spec.js` - Updated test expectations.

## Verification
- **Unit Tests**: `OrganizationSettings.spec.js` passed with new tier name assertions.
- **Visual Check**: Verified `en.json` structure and removal of duplicates.
