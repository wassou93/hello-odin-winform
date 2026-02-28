# DPI Awareness Scaling Diffs

## checkbox.odin

### Change 1: Lines 64-67 (Size Increments)
```diff
- this.autoSize = true
- this._SizeIncr.width = 20
- this._SizeIncr.height = 3
- this._clsName = &btnclass[0]
+ this.autoSize = true
+ // size increments used when autoSize is enabled - make DPI aware
+ dpi := GetDpiForSystem()
+ this._SizeIncr.width = int(f32(20) * f32(dpi) / 96.0)
+ this._SizeIncr.height = int(f32(3) * f32(dpi) / 96.0)
+ this._clsName = &btnclass[0]
```

### Change 2: Lines 188-194 (Text Offset)
```diff
                    rct: RECT = nmcd.rc
-                   if cb.textAlign == .Left{
-                       rct.left += 18
-                   } else do rct.right -= 18
+                   // add gap between checkbox glyph and text; scale by system DPI
+                   dpi := GetDpiForSystem()
+                   off := int(f32(18) * f32(dpi) / 96.0)
+                   if cb.textAlign == .Left{
+                       rct.left += i32(off)
+                   } else do rct.right -= i32(off)
                    SetTextColor(nmcd.hdc, cref)
```

---

## radiobutton.odin

### Change 1: Lines 97-100 (Size Increments)
```diff
    this._exStyle = 0
-   this._SizeIncr.width = 20
-   this._SizeIncr.height = 3
-   this._clsName = WcRadioBtnClassW
+   // auto-size increments, scaled to system DPI
+   dpi := GetDpiForSystem()
+   this._SizeIncr.width = int(f32(20) * f32(dpi) / 96.0)
+   this._SizeIncr.height = int(f32(3) * f32(dpi) / 96.0)
+   this._clsName = WcRadioBtnClassW
```

### Change 2: Lines 301-307 (Text Offset)
```diff
                    rct: RECT = nmcd.rc
-                   if rb.textAlign == .Left{
-                       rct.left += 18
-                   } else do rct.right -= 18
+                   // gap between radio glyph and text, scaled for DPI
+                   dpi := GetDpiForSystem()
+                   off := int(f32(18) * f32(dpi) / 96.0)
+                   if rb.textAlign == .Left{
+                       rct.left += i32(off)
+                   } else do rct.right -= i32(off)
                    SetTextColor(nmcd.hdc, cref)
```

---

## Summary

These diffs make checkbox and radio button controls DPI-aware by:
1. Scaling the auto-size width/height increments (20 and 3 pixels) using the system DPI
2. Scaling the text offset gap (18 pixels) to prevent text from overlapping the control glyph on high-DPI displays

Formula used: `int(f32(pixels) * f32(dpi) / 96.0)` where 96 is the baseline DPI (100%)
