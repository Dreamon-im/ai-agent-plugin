---
name: macos-app-screenshot
description: Capture a retina screenshot of any macOS app's window by app name. Use when the user asks to screenshot an app, capture UI for documentation or marketing pages, or convert captures to WebP for the web.
---

# macOS App Screenshot

Capture a macOS app's frontmost window as a flat, retina (2x), shadow-free
PNG using the bundled script — no manual window-id hunting.

## Usage

Run the script from this skill's folder:

```bash
scripts/shot <AppName> [output.png]
```

- `AppName` is case-insensitive; a partial match works when it is unambiguous
  ("preview" matches Preview.app). Exact matches win over partial ones.
- Without an output path it saves to `~/Desktop/<App>-<timestamp>.png`.

## Requirements and gotchas

- The terminal running this skill needs **Screen Recording** permission
  (System Settings → Privacy & Security → Screen Recording). If the error is
  `could not create image from window`, that permission is missing — grant it
  and fully restart the terminal app.
- The window must be **visible on screen**; minimized windows cannot be
  captured this way. Window IDs change on every app relaunch, so never cache
  them — resolve by name each time (the script does this).
- `-o` in the script omits the drop shadow; drop that flag if a shadow is
  wanted.

## Tips for marketing-quality captures

- Fix the window size first for consistent shots, e.g.:
  `osascript -e 'tell application "System Events" to tell process "<App>" to set size of front window to {1200, 760}'`
- Stage realistic content before capturing (scan a real folder, populate
  lists) and turn on Do Not Disturb.
- For web use, convert to WebP after capturing:
  `python3 -c "from PIL import Image; Image.open('in.png').convert('RGB').save('out.webp','WEBP',quality=88)"`

## Fallback: resolve a window ID manually

```bash
swift - <<'SWIFT'
import CoreGraphics
let opts = CGWindowListOption([.optionOnScreenOnly, .excludeDesktopElements])
let list = CGWindowListCopyWindowInfo(opts, kCGNullWindowID) as! [[String: Any]]
for w in list where (w["kCGWindowOwnerName"] as? String) == "Deckhand" {
    print(w["kCGWindowNumber"]!, w["kCGWindowBounds"]!)
}
SWIFT
screencapture -x -o -l<id> shot.png
```
