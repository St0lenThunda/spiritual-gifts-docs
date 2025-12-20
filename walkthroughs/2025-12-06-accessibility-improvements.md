# Accessibility & Popover Improvements

**Date**: December 6, 2025  
**Status**: Completed ✅

## Summary

Enhanced the frontend application with accessibility improvements and refined scripture popover functionality, including a version selector dropdown.

## Accessibility Enhancements

### High Contrast Mode

Implemented a high-contrast mode toggle that:
- Applies the Atkinson Hyperlegible font for improved readability
- Increases contrast ratios for text elements
- Persists user preference in localStorage

```javascript
// Toggle high contrast mode
const toggleHighContrast = () => {
  document.body.classList.toggle('high-contrast')
  localStorage.setItem('highContrast', document.body.classList.contains('high-contrast'))
}
```

### CSS Variables for Contrast

```css
.high-contrast {
  --text-primary: #ffffff;
  --text-secondary: #e0e0e0;
  --bg-contrast: #000000;
  font-family: 'Atkinson Hyperlegible', sans-serif;
}
```

### Other Accessibility Improvements

- Ensured proper focus states on interactive elements
- Added ARIA labels where needed
- Improved keyboard navigation
- Verified color contrast ratios meet WCAG guidelines

## Scripture Popover Refinements

### Version Selector Dropdown

Replaced the previous display of all scripture versions with a single dropdown menu:

```vue
<template>
  <div class="popover-header">
    <span class="reference">{{ reference }}</span>
    <select v-model="selectedVersion" class="version-select">
      <option v-for="version in availableVersions" :key="version">
        {{ version }}
      </option>
    </select>
  </div>
  <div class="popover-content">
    {{ scriptureText }}
  </div>
</template>
```

### Features

- **Right-justified dropdown** in the popover header
- **Persisted selection** - User's preferred version is saved and used across all popovers
- **Single version display** - Only shows text for the currently selected version
- **Available versions**: ESV, NIV, KJV, NASB

### Implementation Details

```javascript
// Persist user's preferred Bible version
const preferredVersion = ref(localStorage.getItem('bibleVersion') || 'ESV')

watch(preferredVersion, (newVersion) => {
  localStorage.setItem('bibleVersion', newVersion)
})

const scriptureText = computed(() => {
  return scriptures[props.reference]?.[preferredVersion.value] || ''
})
```

## Files Modified

- `frontend/src/components/ScripturePopover.vue` - Dropdown selector, persistence
- `frontend/src/assets/main.css` - High contrast styles
- `frontend/src/App.vue` - High contrast toggle
- Various components - Focus states, ARIA labels

## Testing

### Manual Verification
- Tested high contrast mode toggle
- Verified font changes apply correctly
- Tested popover dropdown on all scripture references
- Confirmed version preference persists across page reloads

### Lighthouse Audit
- Ran accessibility audit
- Addressed any flagged issues
