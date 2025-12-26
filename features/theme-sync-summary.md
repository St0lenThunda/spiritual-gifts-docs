# Theme Sync with Admin CSS Presets - Summary

**Date**: December 26, 2025  
**Status**: ✅ Partially Complete (needs CSS preview update)

---

## Changes Completed

### 1. ThemePreferences.vue - Theme List ✅
**Updated the theme list to match actual CSS classes:**

```javascript
const allThemes = ref([
  // FREE TIER (3 themes)
  { id: 'light', name: 'Salt & Light', description: 'Classic professional light', tier: 'free' },
  { id: 'dark', name: 'Midnight Sanctuary', description: 'Classic dark mode', tier: 'free' },
  { id: 'synthwave', name: 'Synthwave', description: 'Retro neon vibes', tier: 'free' },
  
  // INDIVIDUAL TIER (2 additional = 5 total)
  { id: 'living-water', name: 'Living Water', description: 'Refreshing cyan blues', tier: 'individual' },
  { id: 'doves-wing', name: "Dove's Wing", description: 'Gentle purple & amber', tier: 'individual' },
  
  // MINISTRY TIER (2 additional = 7 total)
  { id: 'celestial', name: 'Celestial Night', description: 'Modern navy & silver', tier: 'ministry' },
  { id: 'sacred', name: 'Sacred Grapes', description: 'Deep purple & bronze', tier: 'ministry' }
]);
```

---

## Mapping: Old → New

| Old Theme | New Theme | Tier | CSS Class |
|-----------|-----------|------|-----------|
| light | **Salt & Light** | free | `:root` (default) |
| dark | **Midnight Sanctuary** | free | `.theme-dark` |
| synthwave | **Synthwave** | free | `.theme-synthwave` |
| ocean |→ **Living Water** | individual | `.theme-living-water` |
| forest | ❌ Removed | - | - |
| sunset | ❌ Removed | - | - |
| - | **Dove's Wing** (New) | individual | `.theme-doves-wing` |
| midnight | ❌ Removed (merged with dark) | - | - |
| glacier | ❌ Removed | - | - |
| autumn | ❌ Removed | - | - |
| minimal | ❌ Removed | - | - |
| - | **Celestial Night** (New) | ministry | `.theme-celestial` |
| - | **Sacred Grapes** (New) | ministry | `.theme-sacred` |

**Total**: 7 themes (was 10)
  - Free: 3 themes
  - Individual: 5 themes (+2)
  - Ministry: 7 themes (+2)
  - Church: All themes

---

## Remaining Tasks

### 1. Update ThemeCard.vue Preview Colors
**File**: `frontend/src/components/settings/ThemeCard.vue`

**Need to update** lines 87-97:
```javascript
const colors = {
  light: { primary: '#f8fafc', secondary: '#e2e8f0' },
  dark: { primary: '#020617', secondary: '#0f172a' },
  synthwave: { primary: '#ff2a6d', secondary: '#05d9e8' },
  'living-water': { primary: '#0891b2', secondary: '#0ea5e9' },  // NEW
  'doves-wing': { primary: '#7e22ce', secondary: '#fbbf24' },   // NEW
  celestial: { primary: '#38bdf8', secondary: '#e2e8f0' },      // NEW
  sacred: { primary: '#c084fc', secondary: '#fbbf24' }          // NEW
};
```

**Remove**: ocean, forest, sunset, midnight, glacier, autumn, minimal

### 2. Update applyTheme() Function  
**File**: `frontend/src/stores/user.js`

**Update themeClasses array** (line ~160):
```javascript
const themeClasses = [
  'theme-light', 
  'theme-dark', 
  'theme-synthwave', 
  'theme-living-water',   // NEW
  'theme-doves-wing',     // NEW
  'theme-celestial',      // NEW
  'theme-sacred'          // NEW
];
```

**Remove**: theme-ocean, theme-forest, theme-sunset, theme-midnight, theme-glacier, theme-autumn, theme-minimal

---

## CSS Theme Definitions (from main.css)

All themes are defined in `/frontend/src/assets/main.css`:

1. **`:root`** - Salt & Light (default/light)
2. **`.theme-living-water`** - Cyan ocean blues
3. **`.theme-doves-wing`** - Purple & amber
4. **`.theme-midnight` / `.theme-dark`** - Dark blues
5. **`.theme-celestial`** - Navy & silver
6. **`.theme-sacred`** - Purple & bronze  
7. **`.theme-synthwave`** - Pink & cyan retro

---

## Backend Tier Limits (Already Defined)

**File**: `backend/app/services/entitlements.py`

```python
THEMES_FREE = ["light", "dark", "synthwave"]
THEMES_INDIVIDUAL = THEMES_FREE + ["sunset", "ocean"]      # Need to update
THEMES_MINISTRY = THEMES_INDIVIDUAL + ["forest", "midnight", ...] # Need to update
THEMES_CHURCH = "all"
```

**Should be**:
```python
THEMES_FREE = ["light", "dark", "synthwave"]
THEMES_INDIVIDUAL = THEMES_FREE + ["living-water", "doves-wing"]
THEMES_MINISTRY = THEMES_INDIVIDUAL + ["celestial", "sacred"]
THEMES_CHURCH = "all"
```

---

## Benefits of Sync

| Before | After |
|--------|-------|
| 10 themes (many without CSS) | 7 themes (all have CSS) |
| Generic names | Meaningful biblical names |
| Mismatched CSS/JS | Perfect sync with CSS |
| Some themes don't work | All themes work |
| ocean/forest/etc (generic) | Living Water/Dove's Wing (biblical) |

---

## Next Steps

1. ✅ **ThemePreferences.vue** theme list updated
2. ⏳ **ThemeCard.vue** preview colors - needs manual update
3. ⏳ **user.js** applyTheme() - needs manual update
4. ⏳ **Backend** entitlements.py - needs update for tier enforcement

---

## Testing Checklist

After completing remaining updates:

- [ ] Select "Salt & Light" → background should be very light
- [ ] Select "Midnight Sanctuary" → background should be very dark
- [ ] Select "Synthwave" → should see pink/cyan neon
- [ ] Select "Living Water" → should see cyan blues
- [ ] Select "Dove's Wing" → should see purple/amber
- [ ] Select "Celestial Night" (Ministry) → should see navy/silver  
- [ ] Select "Sacred Grapes" (Ministry) → should see purple/bronze
- [ ] Preview cards should match actual theme colors
- [ ] Tier locks should work correctly

---

**Status**: Theme list synced ✅, CSS preview colors pending ⏳, applyTheme() pending ⏳
