# "Don't Show Again" Feature for Instructions Modal

**Feature**: Added checkbox to Instructions Modal to allow users to hide it on future visits  
**Date**: December 26, 2025  
**Status**: ✅ Complete

---

## Changes Made

### 1. InstructionsModal Component
**File**: `frontend/src/components/InstructionsModal.vue`

**Added**:
- Checkbox at bottom left of modal footer
- Label: "Don't show instructions again"
- Save preference to backend on change
- Toast notification on successful save
- Revert checkbox on error

**UI Layout**:
```
┌──────────────────────────────────────────┐
│           Instructions Modal              │
│                                          │
│  [Content...]                            │
│                                          │
│  ☐ Don't show again     [BEGIN BUTTON]  │
└──────────────────────────────────────────┘
```

### 2. User Preferences Store
**File**: `frontend/src/stores/user.js`

**No changes needed** - Already supports nested `ui` object in preferences

**Preference Structure**:
```javascript
{
  theme: "dark",
  locale: "en",
  sync_across_orgs: true,
  ui: {
    hideAssessmentInstructions: false  // NEW
  }
}
```

### 3. TakeAssessment Page
**File**: `frontend/src/pages/TakeAssessment.vue`

**Changes**:
- Import `useUserStore`
- Load user preferences on mount
- Check `ui.hideAssessmentInstructions` preference
- Show instructions only if NOT hidden

**Logic**:
```javascript
// Default: don't show (will check preference)
const showInstructions = ref(false);

onMounted(async () => {
  // Load and check preference
  await userStore.loadPreferences();
  const hideInstructions = userStore.preferences?.ui?.hideAssessmentInstructions || false;
  showInstructions.value = !hideInstructions;  // Show if NOT hidden
  
  // ... rest of mount logic
});
```

### 4. Translation
**File**: `frontend/src/locales/en.json`

**Added Key**:
```json
{
  "assessment": {
    "dont_show_instructions": "Don't show instructions again"
  }
}
```

---

## User Flow

### First Visit
1. User goes to `/take` (assessment page)
2. Instructions modal appears automatically
3. User reads instructions
4. User checks "Don't show again" (optional)
5. Preference saved to backend
6. Toast: "Preference saved. Instructions will not show again."
7. User clicks "BEGIN"

### Subsequent Visits
1. User goes to `/take`
2. **Modal does NOT appear** (preference respected)
3. User goes straight to assessment

### Resetting Preference
Users can re-enable instructions via:
1. Go to `/settings`
2. Reset preferences to defaults
3. OR manually re-check "show instructions" (if we add that option)

---

## Backend Compatibility

**No backend changes required!**

The existing preferences API already supports nested JSON:
```python
# Backend already handles this
{
  "ui": {
    "hideAssessmentInstructions": true
  }
}
```

Storage: `user.global_preferences` JSON column

---

## Technical Implementation

### Checkbox Component
```vue
<label class="flex items-center gap-2 cursor-pointer group">
  <input
    type="checkbox"
    v-model="dontShowAgain"
    @change="handleDontShowAgainChange"
    class="w-4 h-4 rounded border-gray-600 bg-white/10 text-synth-pink 
           focus:ring-2 focus:ring-synth-cyan cursor-pointer transition"
  />
  <span class="text-sm text-gray-400 group-hover:text-gray-300 transition">
    {{ $t('assessment.dont_show_instructions') }}
  </span>
</label>
```

### Save Handler
```javascript
async function handleDontShowAgainChange() {
  try {
    await userStore.updatePreferences({ 
      ui: { 
        ...userStore.preferences.ui,
        hideAssessmentInstructions: dontShowAgain.value 
      } 
    });
    
    if (dontShowAgain.value) {
      toast.success('Preference saved. Instructions will not show again.');
    }
  } catch (error) {
    console.error('Failed to save preference:', error);
    toast.error('Failed to save preference');
    // Revert on error
    dontShowAgain.value = !dontShowAgain.value;
  }
}
```

---

## Files Modified

1. ✅ `frontend/src/components/InstructionsModal.vue` - Added checkbox UI and logic
2. ✅ `frontend/src/pages/TakeAssessment.vue` - Check preference before showing
3. ✅ `frontend/src/locales/en.json` - Added translation key

**Total**: 3 files modified

---

## Testing

### Manual Test
1. Navigate to `/take`
2. Instructions modal should appear
3. Check "Don't show again"
4. Toast confirms: "Preference saved"
5. Click "BEGIN"
6. Refresh page or navigate away and back
7. **Modal should NOT appear**
8. Go to `/settings` → Reset preferences
9. Navigate to `/take`
10. **Modal should appear again**

### Edge Cases
- ✅ Unchecking should allow modal to show again (saves false)
- ✅ Error during save reverts checkbox
- ✅ New users (no preference set) see modal by default
- ✅ Works across sessions (preference persisted)

---

## Future Enhancements

### Phase 2 (Optional)
1. **Settings Page Toggle**: Add explicit UI preference in Settings
2. **"Show Instructions" Link**: Add link in assessment to re-open modal
3. **Keyboard Shortcut**: Allow `?` key to open instructions anytime
4. **First-time Only**: Show once per user automatically, then hide

---

## Benefits

| Before | After |
|--------|-------|
| Modal shows every visit | User control over visibility |
| No way to disable | Simple checkbox |
| Annoying for repeat users | Smooth UX for experienced users |
| N/A | Preference saved to backend |

---

##User Experience

**First-time users**: Get helpful instructions  
**Repeat users**: Can skip straight to assessment  
**Power users**: Can permanently hide (preference saved)  
**Flexibility**: Can always reset via Settings

---

**Status**: ✅ Production Ready  
**Breaking Changes**: None  
**Backward Compatible**: Yes (new users default to showing modal)  
**Backend Changes**: None required

