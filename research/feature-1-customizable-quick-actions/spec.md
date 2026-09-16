# Feature 1 Spec: Customizable Quick Actions

## Pain point

Pinterest's long-press/hover quick-action menu is fixed at 4 actions (See less, See more, Share, Save) with no way to customize it. Power users have different priorities — someone who mainly collects reference images wants Download front and center; someone who's social wants Share and React up top.

## Platform split (resolved decision)

Desktop web has no long-press gesture, so this feature needs two interaction surfaces sharing one settings config:

- **Desktop (mouse/pointer):** hovering a pin reveals a quick-action bar of buttons on the card, matching Pinterest's own desktop hover pattern.
- **Mobile/touch:** long-pressing a pin surfaces the same actions as a popup/bottom-sheet menu, matching Pinterest's native app pattern.

**Detection rule:** use `matchMedia('(hover: hover) and (pointer: fine)')`, not a screen-width breakpoint — a touch-capable laptop or an iPad with a trackpad should still get the interaction that matches its actual input method, not its screen size.

## Action set

See [action-inventory.md](action-inventory.md) for the full reconciliation. Pending team sign-off, the working set is 8 actions: Save, Share, Download image, Hide pin, Report, Copy link, React, Add note.

## Slots + presets (adopted from the POC)

Rather than a flat checkbox list, the settings screen offers:
- A **slot count** of 3, 4, or 5 visible actions on the bar/menu — anything beyond the slot count still exists in an overflow ("...") menu, just not on the primary bar.
- Three **presets** users can apply in one click:
  - **Creator** (4 slots): Save, Download, Add note, React *(swap Find similar → React per the trimmed action list)*
  - **Collector** (3 slots): Save, Download, Copy link
  - **Social** (5 slots): Save, Share, React, Copy link *(drop Send to friend if that action is cut — see action-inventory.md open item)*
- A manual mode where the user checks/unchecks individual actions instead of using a preset.

## Defaults

If a user has never customized anything, both surfaces show the original Pinterest 4: See less, See more, Share, Save — **wait, these are cut per the action inventory.** Resolved default instead: **Save, Share, Download image, Hide pin** (closest match to Pinterest's spirit using only actions in the trimmed set). This needs explicit team confirmation since it changes the "matches current Pinterest" framing from the original spec.

## Settings behavior

- One settings page controls both desktop and mobile surfaces from the same stored config — one config, two renderers.
- Stored in `localStorage` (single JSON blob, see [../features.md](../features.md) cross-feature decision on a shared preferences store) — explicitly not synced across devices for this demo.
- Minimum-actions rule: at least 1 action must always be selected; block the last one from being deselected.
