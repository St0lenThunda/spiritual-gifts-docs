# Admin Sidebar Subscription Level

**Date**: January 2, 2026
**Status**: Completed ✅

## Summary
Added a stylized "Current Plan" indicator to the bottom of the Admin Sidebar to give administrators visibility into their organization's subscription tier.

## Changes
- **Frontend**: Modified `AdminSidebar.vue` to fetch the organization's plan from the store and display it with a gradient text effect.
- **Localization**: Added `admin.sidebar.current_plan` and `plan_label` keys to `en.json`, `es.json`, `fr.json`, and `ru.json`.

## Implementation Details

### `AdminSidebar.vue`
Added a bottom section to the sidebar:
```html
<div class="p-4 border-t border-border-subtle">
   <div class="relative overflow-hidden rounded-xl ...">
     ...
     <p class="font-display font-bold text-lg bg-gradient-to-r from-brand-primary to-brand-accent bg-clip-text text-transparent">
       {{ $t('org.billing.plans.' + orgStore.orgPlan) }}
     </p>
   </div>
</div>
```

### Locales
Added:
```json
"sidebar": {
  "plan_label": "CURRENT PLAN",
  "current_plan": "{plan} Plan"
}
```

## Verification
- Verified correct rendering of the plan name using the `orgStore`.
- Verified translations for all supported languages.
- Ran E2E tests for Admin Dashboard.
