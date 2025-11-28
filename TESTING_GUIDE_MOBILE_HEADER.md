# 🧪 HƯỚNG DẪN TEST - Header Mobile Fix

## 🎯 Mục tiêu Testing
Đảm bảo header không bị scroll ngang trên mọi thiết bị mobile

---

## 📱 Test Plan

### 1. Chrome DevTools Testing (Desktop)

#### Bước 1: Mở DevTools
```
1. Mở Chrome
2. Vào http://localhost:3000
3. Nhấn F12 hoặc Ctrl+Shift+I
4. Click icon "Toggle device toolbar" (Ctrl+Shift+M)
```

#### Bước 2: Test các device presets
```
Test với các devices:
□ iPhone SE (375 × 667)
□ iPhone 12 Pro (390 × 844)
□ iPhone 14 Pro Max (430 × 932)
□ Pixel 5 (393 × 851)
□ Galaxy S20 Ultra (412 × 915)
□ Galaxy S8+ (360 × 740)
□ iPad Mini (768 × 1024)
□ iPad Air (820 × 1180)
```

#### Bước 3: Kiểm tra scroll
```
Cho mỗi device:
1. ✅ Check: Không có scrollbar ngang
2. ✅ Check: Tất cả buttons visible
3. ✅ Check: Logo không bị cắt
4. ✅ Check: Logout button không tràn
5. ✅ Check: Hover states work
```

---

### 2. Manual Testing Checklist

#### A. Guest User (Không đăng nhập)
```
Viewport: 375px
Expected buttons:
□ [📋] History button
□ [🌙] Theme toggle
Total: 2 buttons + Logo

Expected width: ~200px
Status: Should fit easily ✅
```

#### B. Staff User (Đã đăng nhập)
```
Viewport: 375px
Expected buttons:
□ [🛒] Pending Orders (with badge)
□ [✓] Merged Orders
□ [📊] Summary Report
□ [⚙] Dashboard
□ [📋] History
□ [🌙] Theme
□ [Thoát] Logout

Total: 7 items + Logo

Expected width: ~360px
Status: Should fit in 375px ✅
```

#### C. Admin User (Full access)
```
Viewport: 375px
Expected buttons:
□ [🛒] Pending Orders
□ [✓] Merged Orders
□ [📊] Summary Report
□ [⚙] Dashboard
□ [🔧] Admin Panel
□ [📋] History
□ [🌙] Theme
□ [Thoát] Logout

Total: 8 items + Logo

Expected width: ~362px
Status: Should fit in 375px ✅
```

---

### 3. Responsive Breakpoint Testing

#### Test 1: 360px (Narrowest)
```
Device: Galaxy S8+
Expected:
✅ All icons 20px (w-5 h-5)
✅ Container padding 8px (px-2)
✅ Button gap 4px (gap-1)
✅ Logo 24px (text-2xl)
✅ Logout text "Thoát"
✅ User info hidden
✅ No horizontal scroll
```

#### Test 2: 640px (Small Tablet)
```
Expected transition at 640px:
✅ Icons grow to 28px (w-7 h-7)
✅ Container padding 16px (px-4)
✅ Button gap 8px (gap-2)
✅ Logo 30px (text-3xl)
✅ Logout text "Đăng xuất"
✅ User info still hidden
```

#### Test 3: 768px+ (Tablet/Desktop)
```
Expected at 768px+:
✅ User info visible (name + role)
✅ All other elements at desktop size
✅ Plenty of space
```

---

### 4. Dark Mode Testing

#### Steps:
```
1. Click [🌙] button
2. Verify header background changes
3. Check icon colors visible
4. Verify badges still visible
5. Test on mobile viewport
6. Confirm no layout shift

Expected results:
□ Dark mode: bg-stone-900/80
□ Light mode: bg-white/80
□ All icons visible in both modes
□ No scroll in either mode
```

---

### 5. Real Device Testing (Optional but Recommended)

#### iOS Testing:
```
1. Build & deploy to Vercel
2. Open on iPhone
3. Test portrait & landscape
4. Check Safari & Chrome
5. Test 3D Touch (if available)
6. Verify smooth scrolling

Devices:
□ iPhone SE (smallest)
□ iPhone 12/13
□ iPhone 14 Pro Max
```

#### Android Testing:
```
1. Open deployed URL
2. Test Chrome & Samsung Internet
3. Check various screen sizes
4. Test gesture navigation
5. Verify no performance issues

Devices:
□ Small phone (360px)
□ Medium phone (390px)
□ Large phone (430px)
```

---

### 6. Edge Cases Testing

#### Test A: Very Narrow (320px)
```
⚠️ Not officially supported but should work:
- Icons might be tighter
- But should not scroll
- All buttons clickable

Result: Should degrade gracefully
```

#### Test B: Landscape Mode
```
1. Rotate device to landscape
2. Header should have more space
3. All elements visible
4. No issues expected
```

#### Test C: Zoom Testing
```
1. Pinch to zoom in (150%, 200%)
2. Should maintain layout
3. Might scroll horizontally when zoomed
   (This is expected browser behavior)
```

---

### 7. Performance Testing

#### Page Load:
```
1. Open Chrome DevTools
2. Go to Network tab
3. Throttle to "Slow 3G"
4. Refresh page
5. Check header renders quickly

Expected:
□ Header visible immediately
□ No layout shift during load
□ Icons load smoothly
```

#### Interaction Testing:
```
1. Click each button rapidly
2. Check for lag or jank
3. Test hover states (desktop)
4. Test touch targets (mobile)

Expected:
□ 40px+ touch target for all buttons
□ Smooth hover transitions
□ No delay in navigation
```

---

### 8. Browser Compatibility

#### Test Browsers:
```
Desktop:
□ Chrome (latest)
□ Firefox (latest)
□ Edge (latest)
□ Safari (if Mac available)

Mobile:
□ Chrome Mobile
□ Safari iOS
□ Samsung Internet
□ Firefox Mobile
```

---

### 9. Accessibility Testing

#### Keyboard Navigation:
```
1. Tab through all header buttons
2. Each should have focus ring
3. Enter/Space should activate
4. Focus order should be logical

Expected order:
Logo → Pending → Merged → Summary → Dashboard → Admin → History → Theme → Logout
```

#### Screen Reader:
```
1. Turn on screen reader
2. Navigate header
3. Each button should announce properly

Expected:
□ "Đơn chờ xử lý" button
□ "Đơn đã chốt" button
□ etc.
```

---

### 10. Production Deployment Testing

#### Pre-Deploy Checklist:
```
□ Run `npm run build`
□ Check bundle size
□ Preview with `npm run preview`
□ Test preview on mobile
□ Verify no console errors
□ Check all routes work
```

#### Post-Deploy Checklist:
```
□ Test Vercel URL on mobile
□ Check multiple devices
□ Verify all user roles
□ Test dark mode
□ Check performance
□ Monitor for user feedback
```

---

## 🐛 Bug Report Template

If you find issues, use this template:

```markdown
### Bug: Header Issue on Mobile

**Device:** [e.g., iPhone 12, 390px]
**Browser:** [e.g., Safari 16]
**User Role:** [Guest/Staff/Admin]
**Mode:** [Light/Dark]

**Expected:**
[What should happen]

**Actual:**
[What actually happens]

**Screenshot:**
[Attach screenshot]

**Steps to Reproduce:**
1. [First step]
2. [Second step]
3. [Result]

**Severity:** [Low/Medium/High/Critical]
```

---

## ✅ Final Sign-Off Checklist

Before marking as complete:

```
Core Functionality:
□ No horizontal scroll on 375px viewport
□ All buttons visible on 360px viewport
□ Header responsive on all breakpoints
□ Dark mode works correctly
□ All user roles tested (Guest/Staff/Admin)

Performance:
□ Build successful
□ No console errors
□ Fast load time
□ Smooth animations

Documentation:
□ Fix documented
□ Testing guide created
□ Code comments clear
□ README updated (if needed)

Production Ready:
□ Code reviewed
□ Tests passed
□ Ready to deploy
□ Monitoring plan in place
```

---

## 📊 Test Results Template

```
Date: ___________
Tester: ___________

| Device | Viewport | Guest | Staff | Admin | Dark Mode | Pass/Fail |
|--------|----------|-------|-------|-------|-----------|-----------|
| iPhone SE | 375 | ☐ | ☐ | ☐ | ☐ | ☐ |
| iPhone 12 | 390 | ☐ | ☐ | ☐ | ☐ | ☐ |
| Galaxy S20 | 360 | ☐ | ☐ | ☐ | ☐ | ☐ |
| Pixel 5 | 393 | ☐ | ☐ | ☐ | ☐ | ☐ |
| iPad Mini | 768 | ☐ | ☐ | ☐ | ☐ | ☐ |

Overall Status: ☐ PASS / ☐ FAIL
Notes: ___________________________________________
```

---

## 🚀 Quick Test Commands

```bash
# Start dev server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Run on network (test from phone)
npm run dev -- --host

# Then open on phone:
# http://[your-computer-ip]:3000
```

---

## 💡 Tips for Effective Testing

1. **Test in Real Conditions**
   - Use actual devices when possible
   - Test on real networks (not just localhost)
   - Test during different times of day

2. **Document Everything**
   - Screenshot any issues
   - Note device/browser versions
   - Record steps to reproduce

3. **Test Edge Cases**
   - Very small screens (320px)
   - Very large screens (2560px)
   - Slow networks
   - High zoom levels

4. **Get User Feedback**
   - Ask staff to test on their phones
   - Watch how they interact
   - Note any confusion or difficulties

---

**✅ Happy Testing!**  
**📅 Last Updated:** 28/11/2025  
**🔖 Version:** 1.0

