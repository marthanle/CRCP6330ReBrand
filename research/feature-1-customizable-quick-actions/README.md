# Feature 1 Deep Dive: Customizable Quick Actions

Pinterest's quick-action menu (long-press on mobile, hidden behind "..." on desktop) is fixed and not user-configurable. This feature lets users choose which actions appear in it. This doc is the single source of truth for the feature — spec, decisions, and edge cases together.

## Background: spec vs. POC mismatch (now resolved)

The original written spec listed 9 actions; a teammate's POC (`action-catalog.ts`, recovered from git history at commit `06f4a2d`, later deleted at `0238bbd`) implemented a different set of 10, plus a slots (3/4/5) + presets (creator/collector/social) system the spec never mentioned. Only 6 actions were common to both lists. The decisions below resolve that mismatch — **this replaces the POC's slots/presets system with a simpler flat rule** (see "Selection rules").

## Full action list (10 total)

The customizable list a user can choose from:

1. **Save**
2. **Share**
3. **See more**
4. **See less**
5. **Download image**
6. **Hide pin**
7. **Report**
8. **Copy link**
9. **React**
10. **Add note**

## Default (before any customization)

**Save, Share, See more, See less** — matches Pinterest's real current 4 defaults exactly.

## Selection rules

- User selects a **minimum of 1, maximum of 4** actions.
- Trying to select a 5th action, or deselect the last remaining one, triggers a **blocking modal** the user must dismiss (not a toast) — e.g. "You can select up to 4 actions" / "You must keep at least 1 action." The invalid checkbox toggle does not take effect.
- **Order shown in the menu is fixed catalog order** (the order listed above), not the order the user checked them in. Reordering was considered and cut as a stretch goal — not in scope for the class-session build.
- Data behind a deselected action stays intact — e.g. if a user unchecks "Add note" after writing notes, or unchecks "Hide pin" after hiding pins, that underlying data/state is preserved. Removing an action from the quick menu only hides the shortcut, it doesn't delete anything.

## Settings flow: Edit → Checklist → Save

1. **View state:** a "Customizable Quick Actions" row in Settings shows the user's current selection (e.g. "Save, Share, Download image, Hide pin") with an **Edit** control.
2. **Edit state:** tapping Edit opens a checklist of all 10 actions, current selections pre-checked.
3. User toggles checkboxes; the min/max rule is enforced live as described above.
4. **Save** commits the selection and returns to the view state reflecting the update.
5. **Cancel/back without saving:** changes discard silently, no confirmation prompt — simplest option for a single-session build; would want a proper "unsaved changes" safeguard in a real shipped product.

## Platform split: mobile vs. desktop

Desktop web has no long-press gesture, so the two platforms trigger the menu differently, but read from the same saved settings:

- **Mobile (touch):** long-pressing a pin opens the user's chosen actions as a popup/bottom-sheet menu.
- **Desktop (mouse/pointer):** matching real Pinterest's own pattern — hovering a pin reveals a **"..." (three-dot) icon** on the image; clicking it opens the same chosen actions as a menu. The three-dot icon itself is hover-revealed, not always visible — keeps the grid clean by default, exactly like current Pinterest.
- **Detection:** use `matchMedia('(hover: hover) and (pointer: fine)')` to decide which surface applies, not screen width — a touch-capable laptop or an iPad with a trackpad should get the interaction matching its actual input method.

## Keyboard accessibility (desktop)

Hover-to-reveal has no equivalent for a keyboard-only user, so:
- The three-dot control must be a real `<button>` element, not a hover-only `<div>` — this makes it naturally tabbable.
- Reveal it on **`:focus-visible` as well as `:hover`** in CSS, so tabbing to it actually shows it instead of leaving it invisible-but-focused.
- **Enter/Space** opens the menu (free with a native `<button>`).
- Once open, focus moves into the menu (arrow keys/Tab between actions); **Escape** closes it and returns focus to the three-dot button.

This is a well-known accessible-disclosure pattern, cheap to build in from the start rather than retrofitting later.

## What to reuse from the POC (do not rewrite)

- **`types.ts`** — `ActionId`, `ActionDef` types are reusable once the action list is updated to the 10 above (add back `seeMore`/`seeLess`, drop `findSimilar`/`send` per this doc's final list — or keep the extra POC ones as unused/uncalled if that's less work, team's call).
- **`action-catalog.ts`** — `ACTION_CATALOG` has real working implementations already: Save (toast), Download (blob download w/ new-tab fallback), Share (`navigator.share` w/ clipboard fallback), Copy link, Hide (session-based), Add note (localStorage-backed prompt). Reuse these as-is; just need `seeMore`/`seeLess` implemented (both can be simple toasts/stubs for the demo) and drop `slots`/`PRESETS` since this doc replaces that system with the flat min-1/max-4 rule.
- **`QuickBar.tsx`** — currently renders action buttons directly on `.pin:hover`, plus a separate ellipsis for overflow. **Needs updating to match the new desktop pattern:** stop rendering the inline `order.map` buttons on hover; only show the three-dot icon on hover, and open the existing `pop-menu` (already built) on click instead of on ellipsis-click-for-overflow-only. Most of the popup markup is already there — this is a trigger-condition change, not a rewrite.
- **`use-settings.ts`** — reuse for reading/writing the selected-actions list to `localStorage`; drop any slot-count state it manages.
- **`app/settings/page.tsz`** — check before building a new settings row/screen from scratch; UI structure likely already exists and can be adapted to the Edit → Checklist → Save flow.

## New work needed (not in the POC)

1. Mobile long-press handler — doesn't exist yet. Needs a touch handler with a ~500ms hold timer (`onTouchStart` starts it, `onTouchEnd`/`onTouchCancel` clears it), and the timer must cancel if the finger moves more than a few pixels (so scrolling doesn't accidentally trigger it). On success, reuse the same action-list markup as the desktop popup.
2. Pointer-capability detection (`matchMedia`) wired up at the pin-card level to choose which surface is active.
3. The Edit → Checklist → Save settings flow, with the blocking-modal min/max validation.
4. Keyboard accessibility on the three-dot button/popup, as specified above.

## Edge cases to test before calling this done

- **Touch device with a mouse/trackpad** (touchscreen laptop, iPad + trackpad): must use `matchMedia`, not screen width, or it gets the wrong interaction.
- **Long-press cancels on scroll:** moving the finger during the hold must cancel the timer, or every scroll over a pin accidentally opens the menu.
- **Missing/broken pin image:** Download and similar actions shouldn't throw — no-op with a toast ("Image unavailable").
- **Corrupted/missing localStorage on load:** fall back to the default (Save, Share, See more, See less), don't render an empty menu.
- **Last action deselected / 5th action selected:** blocked with the modal described above — verify it actually blocks the toggle, not just shows a warning after the fact.
- **Settings change must affect both surfaces:** test explicitly — change settings, then verify both the desktop three-dot menu (wide viewport) and the mobile long-press menu (narrow/touch-emulated viewport) reflect the change, since they're two separate renderers reading the same config.
- **Keyboard-only flow end to end:** tab to a pin's three-dot button, confirm it becomes visible on focus, open with Enter, navigate the menu, close with Escape, confirm focus returns to the button.
- **Ellipsis/menu positioning near the edge of the screen:** confirm it doesn't render off-screen for pins near the right edge of the grid.
