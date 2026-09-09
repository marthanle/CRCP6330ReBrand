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
