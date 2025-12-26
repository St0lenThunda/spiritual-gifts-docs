# Theme Selection Not Working - Diagnostic Guide

## Issue
Theme cards are not responding when clicked.

## Checklist

### 1. Check Browser Console
Open browser DevTools (F12) and check Console tab for:
- ✅ Any JavaScript errors (red text)
- ✅ Log messages starting with `[ThemePreferences]`
- ✅ Network errors (failed API calls)

### 2. Verify You're Logged In
- Go to `/login` and ensure you're authenticated
- Check if user avatar appears in top-right
- If not logged in, theme selection won't work

### 3. Hard Refresh Browser
The browser might be caching old JavaScript:
```
Ctrl + Shift + R (Windows/Linux)
Cmd + Shift + R (Mac)
```

### 4. Check Dev Server
Ensure dev server is running and shows no errors:
```bash
# Should see:
✓ ready in XXXms
```

### 5. Manual Test Clicks
When clicking a theme card, you should see in console:
```
[ThemePreferences] selectTheme called with: dark
[ThemePreferences] Applying theme optimistically
[ThemePreferences] Saving to backend
[ThemePreferences] Theme saved successfully
```

### 6. Common Issues

#### Issue: Console shows "Theme not available"
**Cause**: Theme locked for your tier
**Expected**: Should show toast: "This theme requires a higher tier"

#### Issue: Console shows "Failed to save theme: 401"
**Cause**: Not authenticated
**Solution**: Go to `/login` and log in again

#### Issue: Console shows "Failed to save theme: 403"
**Cause**: Theme not allowed for your tier
**Check**: Are you trying to select a premium theme on Free tier?

#### Issue: No console logs at all
**Possible causes**:
1. Old JavaScript cached - do hard refresh
2. Event handler not attached - check if ThemeCard emits 'select'
3. Component not mounting - check if page loads correctly

### 7. Inspect Element
Right-click on a theme card → Inspect
- Check if `@click` handler exists in the HTML
- Verify class includes `cursor-pointer`
- Ensure it's not `disabled`

### 8. Test with curl
```bash
# Test if API works
curl -X GET http://localhost:8000/api/v1/user/preferences \
  -H "Cookie: access_token=YOUR_TOKEN"
```

## Quick Fix Steps

1. **Hard refresh browser** (Ctrl+Shift+R)
2. **Open console** (F12)
3. **Click a theme**
4. **Check console output**
5. **Report what you see**

## Expected Working Behavior

1. Click theme card
2. Console logs appear
3. Theme changes instantly on page
4. Success toast appears
5. Page refresh keeps theme

## Debug Commands

```bash
# Check if dev server is running
ps aux | grep vite

# Restart dev server
cd frontend
npm run dev

# Check for JavaScript errors in build
npm run build
```

## What to Report

If still not working, report:
1. Any console errors (screenshot or copy text)
2. What you see when clicking (anything?)
3. Are you logged in? (check avatar in top-right)
4. Which browser? (Chrome, Firefox, Safari, Edge)
5. Any toast notifications appearing?

## Emergency Reset

If all else fails:
```bash
# Clear browser cache and local storage
localStorage.clear()

# Hard refresh
Ctrl + Shift + R

# Re-login
Go to /login
```
