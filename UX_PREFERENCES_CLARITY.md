# Cross-Org Sync UX Clarity Guide

**Issue**: User preferences sync logic is implemented correctly, but users may have incorrect mental models about how it works.

**Solution**: Documentation-first approach with tooltips, help text, and clear microcopy.

---

## 🎯 User Expectation Risks

### 1. **"My theme will sync everywhere automatically"**
**Reality**: Only if `sync_across_orgs` is enabled (default: true)  
**Risk**: Users disable sync not realizing it affects all orgs

### 2. **"Admins can override my preferences"**
**Reality**: Admins cannot override individual user preferences  
**Risk**: Users blame admins for preference changes

### 3. **"Organization theme always wins"**
**Reality**: Hierarchy is Org-specific → Global → System Default  
**Risk**: Confusion when org-specific preference overrides global

---

## ✨ Recommended UX Improvements

### 1. Settings Page - Toggle Wording

**Current** (assumed):
```
☑ Sync preferences across organizations
```

**Improved** (Explicit):
```
☑ Use the same theme in all organizations
ℹ️ When disabled, you can choose different themes for each organization
```

**i18n Keys**:
```json
{
  "settings": {
    "sync_toggle_label": "Use the same theme in all organizations",
    "sync_toggle_help": "When enabled, your theme choice applies everywhere. Disable to customize themes per organization.",
    "sync_enabled_tooltip": "Your theme will be the same across all organizations you belong to",
    "sync_disabled_tooltip": "You can choose different themes for each organization"
  }
}
```

---

### 2. Theme Selection - Context-Aware Help Text

#### When Sync is ENABLED (Global Mode)
```
📌 Your Selection
Theme: Dark Mode
📍 Applies to: All Organizations
ℹ️ This theme will be used everywhere
```

**Implementation**:
```vue
<div v-if="syncEnabled" class="info-banner">
  <InformationCircleIcon class="w-5 h-5 text-blue-500" />
  <p class="text-sm text-text-muted">
    {{ $t('settings.global_theme_notice') }}
  </p>
</div>
```

#### When Sync is DISABLED (Org-Specific Mode)
```
📌 Your Selection
Theme: Dark Mode
📍 For: Grace Community Fellowship only
ℹ️ Other organizations will use your global theme (Light Mode) unless customized
```

**i18n Keys**:
```json
{
  "settings": {
    "global_theme_notice": "This theme will be used in all organizations",
    "org_specific_notice": "This theme applies to {{orgName}} only",
    "other_orgs_fallback": "Other organizations will use your global theme ({{globalTheme}}) unless customized"
  }
}
```

---

### 3. First-Time Setup - Onboarding Modal

Show a one-time explanation modal when user first visits `/settings`:

```
┌─────────────────────────────────────────────┐
│  Welcome to Your Preferences! 👋            │
├─────────────────────────────────────────────┤
│                                             │
│  How Theme Preferences Work:                │
│                                             │
│  ✅ Choose Once, Apply Everywhere           │
│     Your theme choice applies to all        │
│     organizations by default.               │
│                                             │
│  🎨 Customize Per Organization              │
│     Toggle off "sync" to use different      │
│     themes for each organization.           │
│                                             │
│  🔒 Your Choice, Always                     │
│     Admins cannot change your preferences.  │
│     You have full control.                  │
│                                             │
│  [Got it!]  [Don't show again]             │
└─────────────────────────────────────────────┘
```

**Implementation**:
```vue
<TransitionRoot :show="showOnboarding" as="template">
  <Dialog>
    <DialogPanel>
      <DialogTitle>{{ $t('settings.onboarding.title') }}</DialogTitle>
      <div class="space-y-4">
        <div v-for="tip in onboardingTips" :key="tip.icon" class="flex gap-3">
          <component :is="tip.icon" class="w-6 h-6 text-brand-accent" />
          <div>
            <h4 class="font-semibold">{{ $t(tip.titleKey) }}</h4>
            <p class="text-sm text-text-muted">{{ $t(tip.descKey) }}</p>
          </div>
        </div>
      </div>
      <button @click="dismissOnboarding">{{ $t('common.got_it') }}</button>
    </DialogPanel>
  </Dialog>
</TransitionRoot>
```

**Storage**:
```javascript
// Store in localStorage
const hasSeenPreferencesGuide = localStorage.getItem('preferences_guide_seen')
if (!hasSeenPreferencesGuide) {
  showOnboarding.value = true
}
```

---

### 4. Inline Help Icons with Popovers

Add small info icons next to key settings:

```vue
<div class="flex items-center gap-2">
  <label>Theme Preference</label>
  <Popover class="relative">
    <PopoverButton>
      <InformationCircleIcon class="w-4 h-4 text-text-muted hover:text-brand-accent" />
    </PopoverButton>
    <PopoverPanel class="absolute z-10 w-64 p-3 bg-bg-surface border rounded-lg shadow-lg">
      <h4 class="font-semibold mb-2">{{ $t('settings.theme_help_title') }}</h4>
      <ul class="space-y-1 text-sm text-text-muted">
        <li>• {{ $t('settings.theme_help_hierarchy') }}</li>
        <li>• {{ $t('settings.theme_help_admin') }}</li>
        <li>• {{ $t('settings.theme_help_privacy') }}</li>
      </ul>
    </PopoverPanel>
  </Popover>
</div>
```

**Help Text Examples**:
```json
{
  "settings": {
    "theme_help_title": "How Themes Work",
    "theme_help_hierarchy": "Your org-specific choice > Your global choice > System default",
    "theme_help_admin": "Admins cannot see or change your preferences",
    "theme_help_privacy": "Your theme choice is private and under your control"
  }
}
```

---

### 5. Visual Hierarchy Indicator

Show the effective theme source:

```
┌─────────────────────────────────────────────┐
│  Current Theme: Dark Mode                   │
│  📍 Source: Your global preference          │
│                                             │
│  Priority Order:                            │
│  1. ⚙️ Grace Community (Not set)           │
│  2. 🌐 All Organizations (Dark Mode) ✓     │
│  3. 📋 System Default (Light Mode)         │
└─────────────────────────────────────────────┘
```

**Implementation**:
```vue
<div class="theme-source-indicator">
  <div class="flex items-center gap-2 mb-3">
    <span class="text-sm font-medium">Current Theme:</span>
    <span class="px-2 py-1 bg-brand-accent/10 text-brand-accent rounded">
      {{ effectiveTheme }}
    </span>
  </div>
  
  <div class="text-xs text-text-muted space-y-1">
    <p class="font-semibold">Priority Order:</p>
    <div v-for="level in themePriority" :key="level.name" class="flex items-center gap-2">
      <component :is="level.icon" class="w-4 h-4" />
      <span :class="level.isActive ? 'text-green-500 font-semibold' : 'text-text-muted'">
        {{ level.label }}
      </span>
      <CheckCircleIcon v-if="level.isActive" class="w-4 h-4 text-green-500 ml-auto" />
    </div>
  </div>
</div>
```

---

### 6. Org Switcher Dropdown - Theme Preview

When switching orgs via dropdown, show theme preview:

```
┌─────────────────────────────────────────────┐
│  Organizations                              │
├─────────────────────────────────────────────┤
│  ✓ Grace Community Fellowship              │
│    Theme: Dark Mode (Custom)               │
│                                             │
│  → Neon Evangelion Ministry                │
│    Theme: Light Mode (Global)              │
│                                             │
│  → Another Church                           │
│    Theme: Synthwave (Custom)               │
└─────────────────────────────────────────────┘
```

**Implementation**:
```vue
<MenuItem v-for="org in organizations" :key="org.id">
  <button class="w-full text-left px-4 py-2 hover:bg-bg-muted">
    <div class="font-medium">{{ org.name }}</div>
    <div class="text-xs text-text-muted flex items-center gap-1">
      <SwatchIcon class="w-3 h-3" />
      Theme: {{ getOrgTheme(org.id) }}
      <span v-if="isCustomTheme(org.id)" class="text-brand-accent">(Custom)</span>
      <span v-else class="text-text-muted">(Global)</span>
    </div>
  </button>
</MenuItem>
```

---

### 7. Admin Panel - Analytics Disclaimer

In the admin theme analytics dashboard:

```
┌─────────────────────────────────────────────┐
│  Theme Analytics                            │
├─────────────────────────────────────────────┤
│  ℹ️ Privacy Notice:                        │
│  This shows aggregate theme distribution    │
│  only. Individual user preferences are      │
│  private and cannot be viewed or modified.  │
│                                             │
│  Total Users: 42                            │
│  Most Popular: Dark Mode (60%)              │
└─────────────────────────────────────────────┘
```

---

### 8. Reset Confirmation - Be Explicit

When user clicks "Reset to Defaults":

```
┌─────────────────────────────────────────────┐
│  Reset Preferences?                         │
├─────────────────────────────────────────────┤
│  This will:                                 │
│  • Remove your global theme preference      │
│  • Remove all org-specific preferences      │
│  • Restore sync to enabled (default)        │
│                                             │
│  After reset:                               │
│  • You'll use the system default theme      │
│    (Light Mode) everywhere                  │
│  • You can re-customize anytime             │
│                                             │
│  [Cancel]  [Reset to Defaults]             │
└─────────────────────────────────────────────┘
```

**i18n Keys**:
```json
{
  "settings": {
    "reset_confirm_title": "Reset Preferences?",
    "reset_will_remove_global": "Remove your global theme preference",
    "reset_will_remove_org": "Remove all org-specific preferences",
    "reset_will_enable_sync": "Restore sync to enabled (default)",
    "reset_after_notice": "You'll use the system default theme ({{defaultTheme}}) everywhere",
    "reset_can_customize": "You can re-customize anytime"
  }
}
```

---

## 📋 Implementation Checklist

### Phase 1: Immediate (Low-Hanging Fruit)
- [ ] Update toggle label with explicit wording
- [ ] Add info icon with popover next to sync toggle
- [ ] Add context-aware help text under theme selection
- [ ] Update reset confirmation dialog with detailed explanation

### Phase 2: Enhanced UX
- [ ] Implement onboarding modal for first-time visitors
- [ ] Add visual hierarchy indicator
- [ ] Show theme preview in org switcher
- [ ] Add admin analytics disclaimer

### Phase 3: Polish
- [ ] Create animated tutorial video/GIF
- [ ] Add FAQ section in settings
- [ ] Implement "What's This?" links throughout
- [ ] Add tooltips on hover for all key elements

---

## 🎨 Design Patterns to Use

### Info Icons
```vue
<InformationCircleIcon 
  class="w-4 h-4 text-text-muted hover:text-brand-accent cursor-help" 
  v-tooltip="$t('settings.sync_tooltip')"
/>
```

### Help Banners (Non-Intrusive)
```vue
<div class="bg-blue-500/10 border-l-4 border-blue-500 p-3 rounded-r">
  <div class="flex gap-2">
    <InformationCircleIcon class="w-5 h-5 text-blue-500 flex-shrink-0" />
    <p class="text-sm text-text-main">{{ helpText }}</p>
  </div>
</div>
```

### Accordion FAQ
```vue
<Disclosure v-for="faq in faqs" :key="faq.question">
  <DisclosureButton class="flex w-full justify-between p-3 hover:bg-bg-muted">
    <span>{{ faq.question }}</span>
    <ChevronDownIcon class="w-5 h-5" />
  </DisclosureButton>
  <DisclosurePanel class="px-3 pb-3 text-sm text-text-muted">
    {{ faq.answer }}
  </DisclosurePanel>
</Disclosure>
```

---

## 📚 Suggested FAQ Content

```markdown
### Q: Will my theme choice affect other users?
**A:** No. Theme preferences are personal and private. Other users (including admins) cannot see or change your choice.

### Q: What happens when I switch organizations?
**A:** If sync is enabled, you'll see your global theme everywhere. If sync is disabled and you've set an org-specific theme, that will be used. Otherwise, your global theme applies.

### Q: Can admins override my preferences?
**A:** No. Admins have no access to individual user preferences. Your theme choice is under your complete control.

### Q: What does "sync across organizations" mean?
**A:** When enabled (default), your theme choice applies to all organizations. When disabled, you can choose different themes for each organization you belong to.

### Q: What happens if I reset my preferences?
**A:** All your theme choices (global and org-specific) will be cleared, sync will be re-enabled, and you'll use the system default theme (Light Mode) everywhere. You can re-customize at any time.
```

---

## 🚀 Next Steps

1. **Add i18n keys** for all new help text to `en.json`
2. **Implement Phase 1** improvements (toggle label, popovers, help text)
3. **User test** with 2-3 real users to validate clarity
4. **Iterate** based on feedback
5. **Translate** to ES, FR, RU once wording is finalized

---

**Key Principle**: *"Make the implicit explicit, make the complex simple, make assumptions impossible."*

Every piece of UI should answer:
- What does this do?
- Why would I use it?
- What happens if I change it?
