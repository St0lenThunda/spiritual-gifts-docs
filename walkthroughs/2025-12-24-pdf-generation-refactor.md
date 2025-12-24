# PDF Generation Refactor

**Date**: December 24, 2025
**Status**: Completed ✅

## Summary
Resolved several issues with the results PDF generation, including visual regressions (white-on-white text), incorrect data scaling, and layout bugs.

## Issues Addressed / Features Added
- **White-on-White Text Fixed**: Implemented a full-page background fill (`drawBackground`) using the synthwave deep color. All text is now visible and consistently styled.
- **Corrected Score Scaling**: Updated the score bar denominator from 20 to 40 to match the current assessment scoring logic.
- **Improved Page-Break Logic**: Added height estimation and automated page breaks for the "Understanding Your Top Gifts" section, ensuring long definitions are not cut off.
- **Themed PDF Support**: Introduced "Digital (Synth)" and "Print (Light)" themes. Users can now choose a white-background version to save ink when printing physical copies.
- **Robust Data Matching**: Normalized gift name matching to handle potential whitespace or casing mismatches between the results and definition data.
- **Multi-page Footers**: Added per-page footer numbering and generation attribution.

## Implementation Details
- **jsPDF API Update**: Standardized on `doc.getNumberOfPages()` and `doc.setPage()` for reliable page management.
- **Layout Calculations**: Implemented `heightNeeded` estimation before rendering sections to trigger defensive page breaks.

## Files Created/Modified
- `frontend/src/utils/pdfGenerator.js` - Refactored for better layout and visibility.
- `frontend/src/utils/__tests__/pdfGenerator.spec.js` - Updated mocks and added test coverage for page-break related methods.

## Verification
- **Unit Tests**: 100% pass rate on `pdfGenerator.spec.js` (8 tests).
- **Production Build**: Successful build verified.
