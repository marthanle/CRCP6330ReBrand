# Feature 1 Implementation Plan

## What to reuse from the POC (do not rewrite)

The POC (recovered from git history — see repo commit `06f4a2d`, later deleted at `0238bbd`) already has working code that should be the starting point:

- **`types.ts`** — `ActionId`, `ActionDef`, `SlotCount`, `QuickActionSettings` types are solid as-is (once the action list is trimmed per [action-inventory.md](action-inventory.md)).
- **`action-catalog.ts`** — `ACTION_CATALOG`, `ALL_ACTIONS`, `DEFAULT_SETTINGS`, `PRESETS` — real working implementations for Save (toast), Download (actual blob download with new-tab fallback), Share (uses `navigator.share` with clipboard fallback), Copy link, Hide (session-based), Add note (localStorage-backed prompt). Only needs the trimmed action list applied and defaults updated.
- **`QuickBar.tsx`** — already renders an ordered action bar + ellipsis overflow menu, triggered by `.pin:hover` in `globals.css`. This is the desktop surface. Needs to read `order`/`slots` from shared settings instead of a prop passed in from a parent that may be hardcoded — check `PinCard.tsx` and `app/feed/page.tsx` wiring before assuming this is dynamic already.
- **`app/settings/page.tsx`** — likely already has UI for this; read it before building a new settings screen from scratch.
- **`use-settings.ts`** — a settings hook already exists; likely already reads/writes to `localStorage`. Reuse it for the shared preferences store rather than writing a new one.

## What to build new

1. **Trim the action catalog** per the team's decision from [action-inventory.md](action-inventory.md) — remove Find similar (and Send to friend, pending the send-to-board decision), update `DEFAULT_SETTINGS` and `PRESETS` to match.
2. **Mobile long-press handler** — does not exist in the POC. Needs:
   - A touch handler with a ~500ms hold timer (`onTouchStart` starts timer, `onTouchEnd`/`onTouchCancel` clears it).
   - Cancel the timer if touch movement exceeds a few pixels (so scrolling doesn't accidentally trigger it).
   - On successful long-press, open the same action list already used by the ellipsis/overflow menu in `QuickBar.tsx` — reuse that markup as a bottom-sheet or popup rather than building new UI.
3. **Pointer-capability detection** — wire up `matchMedia('(hover: hover) and (pointer: fine)')` at the `PinCard` level to decide which surface (hover bar vs. long-press) is active for the current input method.
4. **Minimum-actions guard** — in the settings UI, disable/block unchecking the last remaining action.

## Suggested build order (for a single team member or pair)

1. Recover and trim the POC files (`types.ts`, `action-catalog.ts`) — 15 min.
2. Wire `QuickBar.tsx` to read live settings instead of a static prop, verify hover bar updates when settings change — 30 min.
3. Build the mobile long-press handler, reusing existing action-list markup — 45–60 min.
4. Add pointer-capability detection to switch between the two surfaces — 15 min.
5. Add the minimum-actions guard to the settings screen — 15 min.
6. Test against the edge cases in [edge-cases.md](edge-cases.md) — 20–30 min.

Total: roughly 2.5–3 hours, consistent with the original per-feature time budget for the class session.
