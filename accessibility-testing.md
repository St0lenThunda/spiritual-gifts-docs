# Accessibility Testing Guide

## vue-axe (Development)

vue-axe provides real-time accessibility feedback during development.

### What You'll See

When running `npm run dev`, open your browser console and look for:

```
♿ vue-axe accessibility testing enabled
```

### Viewing Issues

**Browser Console:**
- Accessibility violations appear as console errors
- Each violation shows: rule ID, impact level, affected elements, and how to fix

**Example output:**
```
[vue-axe] Violations found:
  - color-contrast (serious): Element has insufficient color contrast
    Help: https://dequeuniversity.com/rules/axe/4.4/color-contrast
    Elements: button.submit-btn
```

### Navigating Issues

1. Look for **red error messages** in console
2. Click the **element reference** to highlight in DOM
3. Follow the **Help URL** for WCAG guidance

### Rules Enabled

| Rule | What It Checks |
|------|----------------|
| `color-contrast` | Text meets 4.5:1 contrast ratio |
| `label` | Form inputs have associated labels |
| `image-alt` | Images have alt text |
| `button-name` | Buttons have accessible names |
| `link-name` | Links have accessible names |

---

## Playwright E2E Tests

Run accessibility tests with:

```bash
cd frontend && npx playwright test accessibility.spec.js
```

### Tests Included

| Test | Description |
|------|-------------|
| Homepage a11y | Checks for critical WCAG violations |
| Gifts page | Public content accessibility |
| Login forms | Form label associations |
| Assessment | Accessible form controls |
| Color contrast | Text readability |
| Buttons/Links | Accessible names |
| Images | Alt text presence |

### Adding New Page Tests

```javascript
test('my-page has no a11y violations', async ({ page }) => {
  await page.goto('/my-route');
  const results = await new AxeBuilder({ page })
    .withTags(['wcag2a', 'wcag2aa'])
    .analyze();
  
  expect(results.violations).toEqual([]);
});
```

---

## WCAG Quick Reference

| Level | Meaning |
|-------|---------|
| A | Basic accessibility |
| AA | **Required** (most organizations target this) |
| AAA | Enhanced accessibility |

The app targets **WCAG 2.1 AA** compliance.
