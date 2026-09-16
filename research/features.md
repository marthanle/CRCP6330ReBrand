# Confirmed Feature Updates

Team's 3 feature updates for the Pinterest rebrand. See [pinterest-research.md](pinterest-research.md) for background research.

## 1. Customizable Quick Actions

> **Resolved decision (settled after the POC diverged from this spec):** the original spec described a single "long-press" interaction, but desktop web has no long-press gesture — Pinterest's own desktop site reveals its quick actions on hover, and its native apps use long-press on touch. So this feature needs **two interaction models, one shared settings/config layer**. This is not a new idea invented after the fact — it's just making explicit what Pinterest already does across platforms, and what the POC (`QuickBar.tsx`) already independently arrived at for desktop.

**Default behavior (matches current Pinterest, per platform):**
- **Desktop (mouse/pointer):** hovering a pin reveals a quick-action bar of buttons directly on the card, matching Pinterest's own desktop hover pattern.
- **Mobile/touch:** long-pressing a pin surfaces the same 4 actions as a popup menu, matching Pinterest's native app pattern.
- Both start with the same 4 defaults: **See less, See more, Share, Save**.

**New behavior:** A single settings page controls both surfaces from one customization list, choosing from the full set of actions Pinterest already supports elsewhere in the app (not new, invented actions):
- See less
- See more
- Share
- Save
- Download image
- Hide pin
- Report pin
- Copy link
- Send to board directly

*(Note: the POC's `action-catalog.ts` already implements a slightly larger set — also React, Send to friend, Add note, Find similar. Team should confirm which of these stay in scope rather than silently dropping the ones not listed above.)*

Users pick which actions appear and (stretch goal) reorder them. The same selected-action list drives both the desktop hover bar and the mobile long-press menu — one config, two renderers. If not customized, both stay at the Pinterest default of 4 shown above.

**Implementation notes for MVP:**
- Settings screen: checkbox list of the action options above, with the 4 defaults pre-checked. Selection stored once (e.g. `localStorage`) and read by both surfaces.
- **Desktop:** `QuickBar.tsx` from the POC is the correct foundation, not a rewrite — it already renders an ordered action bar plus an ellipsis/overflow menu for actions not pinned to the bar, triggered by `.pin:hover`. It just needs to read its `order` prop from the shared settings instead of any hardcoded list (check current wiring in `PinCard.tsx` before assuming this is already done).
- **Mobile:** needs a new touch handler (long-press timer, e.g. `onTouchStart`/`onTouchEnd` with a ~500ms hold) that opens the existing action list as a bottom-sheet or popup menu — this can likely reuse `ACTION_CATALOG` and the `pop-menu` markup already in `QuickBar.tsx` rather than building new UI from scratch.
- No real functionality needed behind each action for the demo (e.g., Download can just show a toast) — the point being demoed is the customization flow, not the actions themselves.
- Test both surfaces before calling this feature done — a settings change should visibly affect both the desktop hover bar and the mobile long-press menu.

**Precedent & supporting research:**
- No mainstream social/creative app has shipped a *user-customizable* long-press/context-action menu with a picker UI — this remains a genuine gap, not a re-implementation of an existing feature ([QuickActionView — GitHub](https://github.com/Commit451/QuickActionView)).
- OS-level precedent shows the pattern is well understood by users: macOS lets people enable/disable Quick Actions and assign keyboard shortcuts via System Settings → Extensions ([MacMost](https://macmost.com/customizing-the-mac-context-menu.html)); Windows 11 supports adding custom shortcuts to the right-click context menu ([Windows Central](https://www.windowscentral.com/software-apps/windows-11/how-to-integrate-custom-context-menu-shortcuts-on-windows-11), [Tom's Hardware](https://www.tomshardware.com/software/windows/how-to-add-custom-shortcuts-to-the-windows-11-or-10-context-menu)); iOS Home Screen quick actions work as long-press shortcut menus per app ([Better Programming — iOS 13 Quick Actions](https://betterprogramming.pub/handling-ios-13-quick-actions-67f9e304dcc6)).
- Takeaway: users already have a mental model for "long-press/right-click menu is configurable" from their OS — porting that expectation into Pinterest is a low-learning-curve win.

## 2. Chronological Feed Toggle

**Pain point:** No way to escape the algorithmic feed — users can't just see what's newest from who/what they follow without Pinterest's recommendation engine deciding for them.

**New behavior:** A toggle on the home feed switches between:
- **For You** (default, current Pinterest behavior — algorithmic mix of follows + recommendations)
- **Newest** — strictly chronological, newest-first, showing only pins from boards/people the user follows (no algorithmic injection)

**Implementation notes for MVP:**
- Feed toggle control (e.g. segmented control at top of feed: "For You" / "Newest").
- "Newest" view: sort seeded pin dataset by a `createdAt` timestamp field, descending, filtered to `following: true` sources only.
- No real algorithm needed for "For You" in the demo — existing/current feed order works as-is to contrast against.
- **Mobile/desktop:** single implementation, no platform split needed — this is one shared toggle control and one sort function, just responsive-styled (segmented control can shrink/stack on narrow viewports).

**Precedent & supporting research:**
- X (Twitter) already ships this exact pattern: a "For You" / "Following" tab toggle, where Following shows a reverse-chronological timeline of accounts you follow ([$99 Social](https://www.99dollarsocial.com/blog/benefits-of-twitters-chronological-timeline), [MakeUseOf](https://www.makeuseof.com/tag/switch-chronological-twitter-timeline/)).
- Instagram tested/shipped three feed-sorting options — Home (algorithmic), Favorites, and Following — with the latter two chronological ([PhoneArena](https://www.phonearena.com/news/instagram-chronological-feed-options_id137603), [TechRadar](https://www.techradar.com/news/instagram-is-testing-an-option-to-show-the-latest-posts-first)).
- Caveat worth noting to the team: X recently started algorithmically re-ranking even its "Following" feed by predicted engagement, requiring a further "Switch to Latest" option to get true chronological order ([Social Media Today](https://www.socialmediatoday.com/news/x-formerly-twitter-sorts-following-feed-algorithm-ai-grok/806617/), [PiunikaWeb](https://piunikaweb.com/2026/02/15/x-following-feed-not-in-chronological-order-heres-what-we-know/)) — a reminder to keep our "Newest" mode strictly chronological with no algorithmic re-ranking, since that erosion is exactly the pain point we're fixing.
- Takeaway: this is a validated, well-understood pattern with two major platforms as direct precedent — low risk, high user-recognition.

## 3. Board-View Layout Switcher

**Pain point:** Every Pinterest board renders in the same masonry grid regardless of content. A board of 50 outfits browses fine in masonry, but 12 room designs the user wants to compare side-by-side don't — varied crop heights make comparison hard. Recipes are better as a compact list with titles visible.

**New behavior:** A layout toggle at the top of each board with three options:
- **Masonry** (today's default — varied-height columns)
- **Grid** — uniform square crops, 4 per row on desktop
- **List** — thumbnail + title + tags on one row, best for recipes/articles

**Implementation notes for MVP:**
- Toggle control (3-icon segmented control) at top of board page.
- Three CSS layout modes reading from the same pin dataset — masonry (existing), CSS grid with `object-fit: cover` square crops, and a flex row list view.
- Per-board preference can be stored in local state (stretch: persist per board, default: persist per session).
- **Mobile/desktop:** Grid's "4 per row" is a desktop column count — needs a responsive breakpoint (e.g. 2 per row under ~600px) so it doesn't render illegibly small on phones. Masonry and List already reflow naturally at any width.

**Precedent & supporting research:**
- Notion's database view switcher lets users change how the same underlying items render (table, board, list, gallery, calendar, etc.) without changing the data itself — switching views is purely presentational ([Notion Help](https://www.notion.com/help/views-filters-and-sorts), [Super.so — All Notion Database Views](https://super.so/blog/notion-database-views)); Notion's Board and Gallery views even expose a "Card size" layout control ([Notion Help — Boards](https://www.notion.com/help/boards)).
- Airtable ships six interchangeable views (Grid, Gallery, Kanban, Calendar, Timeline, Form) over the same records, with Gallery view offering a "Customize cards" control for which fields show ([Airtable Support](https://support.airtable.com/docs/getting-started-with-airtable-gallery-views), [Zapier — Airtable Views](https://zapier.com/blog/airtable-views/)).
- Takeaway: "same data, switchable layout" is an established, low-risk UX pattern in productivity tools — our masonry/grid/list switcher applies that same idea to a visual-discovery context where Pinterest currently offers none of it.

---

# Implementation Specs & Edge Cases

Concrete decisions to close the open questions in each feature above, so the team builds from the same assumptions instead of discovering gaps mid-build.

## 1. Customizable Quick Actions

**Action list — decision:** Ship the original 9-action list only (See less, See more, Share, Save, Download image, Hide pin, Report pin, Copy link, Send to board). The POC's extra 4 (React, Send to friend, Add note, Find similar) are out of scope for the demo — cut them from `action-catalog.ts` or leave them coded but unreferenced, team's call, but don't surface them in settings.

**Minimum-actions rule:** A user must always have at least 1 action selected. If they try to uncheck the last one, block it (disable the checkbox or show a small inline message like "Keep at least one action"). An empty quick-bar/long-press menu is a broken state, not a valid customization.

**Reordering — decision:** Cut for the demo. Ship selection only (on/off per action), in the fixed catalog order. Reordering is real added complexity (drag-and-drop, persisting order) for a "nice to have" — call it a stretch goal only if the other two features finish early.

**Settings persistence:** `localStorage`, scoped to the browser/device — explicitly not synced across devices or sessions. State this out loud in the demo ("in a real product this would sync to your account") so it doesn't read as a bug.

**Edge cases:**
- **No pins on the card / broken image:** quick-bar still renders on hover — actions like Download shouldn't crash on a missing `imageUrl`, just no-op with a toast ("Image unavailable").
- **Touch device that also has a mouse (e.g. touchscreen laptop, iPad with trackpad):** detect via pointer capability, not screen width — use `(hover: hover) and (pointer: fine)` media query / `matchMedia` to decide hover-bar vs. long-press, not a device-width breakpoint. Otherwise a large touch tablet gets the wrong interaction.
- **Long-press vs. scroll conflict on mobile:** the touch handler must cancel the long-press timer if the finger moves more than a few pixels (i.e., the user is scrolling, not holding) — otherwise every scroll gesture accidentally triggers the menu.
- **Settings page opened with everything unchecked (shouldn't be reachable, but):** on load, if stored settings are somehow empty/corrupted, fall back to the 4 Pinterest defaults rather than rendering nothing.

## 2. Chronological Feed Toggle

**Empty state — decision:** If "Newest" has zero pins to show (user follows nobody, or follows are silent), show an explicit empty state ("No new pins from people you follow yet — try For You") rather than a blank feed. A blank screen reads as broken in a demo.

**Toggle persistence — decision:** Reset to "For You" on every fresh load/session; don't persist the choice. Keeps the demo simple and avoids a second piece of stored state to explain — flag as a "real product would remember this" note if asked.

**Edge cases:**
- **Ties in timestamp (two pins with identical `createdAt`):** add a stable secondary sort key (e.g. pin ID) so ordering doesn't jitter between renders.
- **Following list changes while toggle is on "Newest":** re-filter live rather than caching a stale follow-list snapshot, so unfollowing someone mid-session removes their pins immediately.
- **Very small following list (1–2 people):** "Newest" should still look intentional, not sparse/broken — worth checking visually with a small seed subset, not just the full 30-pin dataset.

## 3. Board-View Layout Switcher

**Scope of the toggle — decision:** Per-board, not global. Each board remembers its own layout choice (a recipes board defaults differently than a moodboard would over time), stored keyed by board ID in the same local settings store as feature 1. This was left ambiguous in the original spec ("stretch: persist per board") — resolving it now: per-board is the actual target, not a stretch.

**Edge cases:**
- **Board with 0–2 pins:** Grid view (4 per row) with only 1–2 items will look like a layout bug, not a feature — either center/left-align a partial row instead of stretching it, or explicitly test this case so it's not a surprise during the demo.
- **Very long pin titles in List view:** truncate with ellipsis at a fixed line count (e.g. 2 lines) rather than letting text overflow and break the row height.
- **Mobile Grid breakpoint:** confirmed 2 per row under ~600px (already in the spec above) — but verify square crops don't get so small that text/tags become unreadable; if so, drop to a single column on very small phones instead of 2.
- **Switching layout mid-scroll:** preserve scroll position (or at least don't jump to the top jarringly) when the user flips the toggle partway down a long board.

## Cross-Feature Decisions

- **One shared preferences store:** features 1 and 3 both need persisted user/board settings — use a single `localStorage` key (e.g. one JSON blob) rather than each feature inventing its own storage scheme, so there's one place to reset/debug state from.
- **Test with messy data before the demo:** all three features have only been described against clean seed data. Before calling any of them "done," run each against at least one deliberately awkward case — a pin with a missing image, a board with 1 pin, a title long enough to wrap — since demos tend to break on exactly these.
