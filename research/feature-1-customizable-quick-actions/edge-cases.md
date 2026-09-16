# Feature 1 Edge Cases

Things likely to break the demo if untested. Check each before calling this feature done.

## Input detection
- **Touch device with a mouse/trackpad (touchscreen laptop, iPad + trackpad):** must use `matchMedia('(hover: hover) and (pointer: fine)')`, not screen width, or these devices get the wrong interaction (e.g. a hover bar that never triggers because the primary input is touch).
- **Switching input mid-session** (e.g. plugging in a mouse on a touch laptop): ideally re-checks `matchMedia` rather than caching the detection once at page load.

## Long-press vs. scrolling (mobile)
- **Long-press timer must cancel on movement.** If the finger moves more than a few pixels during the hold, treat it as a scroll, not a long-press — otherwise every scroll gesture over a pin accidentally opens the action menu.
- **Long-press timer must cancel on touch-cancel** (e.g. an incoming call, notification pull-down) — don't leave a dangling timer that fires after the interaction is already over.

## Empty / broken data
- **Missing or broken pin image:** Download and other image-dependent actions shouldn't throw — no-op with a toast ("Image unavailable") rather than an unhandled promise rejection.
- **Pin with no `sourceUrl`:** Share and Copy link fall back to `window.location.href` in the POC code already — confirm this fallback still makes sense once real pin data replaces seed data.

## Settings state
- **Last action deselected:** must be blocked in the UI (disable the checkbox or show an inline message) — an empty action bar/menu is a broken state, not a valid customization.
- **Corrupted or missing localStorage value on load:** fall back to `DEFAULT_SETTINGS`, don't render an empty bar.
- **Preset applied, then manually edited, then a different preset applied:** confirm switching presets fully overwrites the manual selection rather than merging with it (avoids a confusing half-preset/half-custom state).

## Cross-surface consistency
- **Settings change must affect both surfaces.** Test explicitly: change settings, then verify both the desktop hover bar (on a wide viewport) and the mobile long-press menu (on a narrow/touch-emulated viewport) reflect the change — don't assume one surface updating means the other did too, since they're two separate renderers reading the same config.

## Visual
- **5-slot bar on a narrow desktop window:** confirm the action bar doesn't overflow or wrap awkwardly if the browser window is resized smaller mid-demo.
- **Ellipsis/overflow menu positioning near the edge of the screen:** confirm it doesn't render off-screen for pins near the right edge of the grid.
