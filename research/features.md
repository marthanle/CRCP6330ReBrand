# Confirmed Feature Updates

Team's 3 feature updates for the Pinterest rebrand. See [pinterest-research.md](pinterest-research.md) for background research.

## 1. Customizable Long-Press Quick Actions

**Default behavior:** Matches current Pinterest exactly — long-pressing a pin shows the 4 existing quick actions: **See less, See more, Share, Save**.

**New behavior:** A settings page lets users customize this menu, choosing from the full list of actions Pinterest already supports elsewhere in the app (not new, invented actions):
- See less
- See more
- Share
- Save
- Download image
- Hide pin
- Report pin
- Copy link
- Send to board directly

Users can pick which actions appear in their long-press menu and (stretch goal) reorder them. If not customized, it stays at the Pinterest default of 4 shown above.

**Implementation notes for MVP:**
- Settings screen: checkbox list of the action options above, with the 4 defaults pre-checked.
- Long-press menu component reads from user's selected action list (stored in local state/localStorage) instead of a hardcoded array.
- No real functionality needed behind each action for the demo (e.g., Download can just show a toast) — the point being demoed is the customization flow, not the actions themselves.

## 2. Chronological Feed Toggle

**Pain point:** No way to escape the algorithmic feed — users can't just see what's newest from who/what they follow without Pinterest's recommendation engine deciding for them.

**New behavior:** A toggle on the home feed switches between:
- **For You** (default, current Pinterest behavior — algorithmic mix of follows + recommendations)
- **Newest** — strictly chronological, newest-first, showing only pins from boards/people the user follows (no algorithmic injection)

**Implementation notes for MVP:**
- Feed toggle control (e.g. segmented control at top of feed: "For You" / "Newest").
- "Newest" view: sort seeded pin dataset by a `createdAt` timestamp field, descending, filtered to `following: true` sources only.
- No real algorithm needed for "For You" in the demo — existing/current feed order works as-is to contrast against.

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
