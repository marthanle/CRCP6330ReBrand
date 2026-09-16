# Feature 2: Chronological Feed Toggle ("For You" / "Newest")

## Summary

A binary toggle on the home feed letting users switch between Pinterest's default
algorithmic feed ("For You") and a strictly chronological feed of pins from
accounts/boards they follow ("Newest"). This is the core mechanic behind
Sourced's "under the user's control, not just the algorithm's" positioning.

## Positioning / Precedent

Sourced sits between two known reference points:

- **Threads** — toggle exists, but algorithm is the default. Closest analog to
  what we're building.
- **Mastodon / Bluesky (default) / BeReal** — chronological-only, no algorithm
  at all. The "purist" end of the spectrum.

| App | Pattern | Takeaway |
|---|---|---|
| X/Twitter | "For you" / "Following" tabs at top of home timeline | Tab metaphor beats a dropdown — one tap to switch, state is visually obvious |
| Instagram | "Following" feed exists but buried 2+ taps deep in a menu | Cautionary tale — most users never find it. Don't bury ours. |
| Threads | "For You" / "Following" toggle at top of home, persists per-session | Closest existing analog to this feature |
| Facebook (2015-era) | "Most Recent" vs "Top Stories" toggle, later removed | Precedent that this pattern *used to* be common and got killed off in favor of algo-only feeds — Sourced explicitly reverses that trend |
| Mastodon / Bluesky | Chronological-only, no algorithm | Philosophical extreme — Sourced makes this optional rather than mandatory |
| BeReal | No feed ranking at all | Same as above, different audience |

**Brand note:** this positioning (optional chronological mode, front-and-center,
not buried) is a direct differentiator from Instagram's version of the same
idea and worth stating explicitly wherever this feature is described publicly.

## Scope

- **What "Newest" includes:** pins from accounts/boards the user follows only.
  `ORDER BY created_at DESC`.
- **What it explicitly does NOT include:** global newest, board-agnostic
  newest, or any ranking/decay/scoring blend. Zero ranking logic — that's the
  entire point of the feature and also the cheapest part to build.
- **What "For You" includes:** unchanged — Pinterest's existing algorithmic
  feed behavior.

## UI / Interaction

- Toggle lives at the **top of the home feed**, not in a settings menu —
  front-and-center is a deliberate call-out against Instagram's failure mode.
- Use a **tab or segmented control**, not a dropdown (see X/Threads precedent).
- Current mode should be visually obvious at a glance (active tab styling).

## Default State & Persistence

- **Default on cold open:** "For You" (matches user expectation from every
  other app the user already uses).
- **Persistence:** remember the last-selected mode per user for at least the
  session; ideally as a stored preference so returning users land where they
  left off. Should **not** reset every time the user navigates away from home
  and back within a session.

## Ordering & Stability

- **Tie-breaking:** two pins saved in the same second fall back to pin ID
  (secondary sort key) for deterministic ordering.
- **Why this matters more here than in a "most liked" sort:** with infinite
  scroll, unstable ordering causes duplicate or skipped pins as new content
  streams in underneath the user.

## Live Updates

- If a followed account posts while the user is scrolled down in "Newest,"
  **do not silently reflow the list** under them.
- Batch new pins behind a **"X new pins" pill** at the top of the feed that
  the user taps to reveal — same pattern as X's timeline.

## Empty State

- New user who follows nobody yet, or follows accounts that haven't posted:
  "Newest" needs a real empty state (e.g. "Follow some boards to see them
  here"), not a blank screen.

## Backend Considerations

- True "Newest" feed architecture at scale needs either:
  - **Fan-out-on-write** — precompute each follower's feed on publish, or
  - **Fan-out-on-read** — query all followed accounts' pins at request time.
- **For the single-session class build:** fan-out-on-read against a small
  seeded/mock dataset is the only feasible path. This does not need to be
  production-architected — just functionally correct against mock data.

## Explicitly Out of Scope for This Build

- Any decay/scoring/relevance blending — that's "For You" territory and scope
  creep.
- Global (non-followed) chronological feed.
- Cross-device sync of the toggle preference (session/local persistence is
  sufficient for the class build).

## Problem Spots & Decision Signals

Places this feature commonly goes wrong, and how to tell which way to go.

**Toggle gets ignored / feels invisible**
- *Problem:* placing it as a small icon or burying it in a menu (Instagram's
  mistake) means most users never discover it, and the feature effectively
  doesn't exist.
- *Signal you're doing it right:* a user who has never seen the feature before
  can find and use it within a few seconds, with no explanation. If you need
  a tooltip or onboarding hint to make it discoverable, the placement is
  wrong — fix the placement before adding an explainer.

**Feed reflows under the user's thumb**
- *Problem:* new pins arriving mid-scroll silently insert themselves above or
  around what the user is looking at, causing mis-taps or lost place.
- *Signal you're doing it right:* nothing on screen ever moves without the
  user taking an action (tapping the "new pins" pill). If you find yourself
  debugging "why did the list jump," that's the tell you skipped the batching
  step.

**"Newest" quietly isn't actually chronological**
- *Problem:* it's tempting to sneak in "a little bit of relevance" to make
  the feed feel less sparse for low-follow users — this defeats the entire
  premise and the brand promise ("not just the algorithm's control").
- *Signal you're doing it right:* you can point at the query and it's a
  single `ORDER BY created_at DESC` (plus the ID tiebreak) with no scoring
  step anywhere in the pipeline. If there's a ranking function involved at
  all, it's not this feature anymore — it's "For You" with extra steps.

**Empty state left as a placeholder**
- *Problem:* new/low-follow users hit a blank "Newest" tab, conclude the
  feature is broken, and never try it again.
- *Signal you're doing it right:* the empty state is designed before the
  populated state, not after. If you're bolting it on at the end because
  "we'll probably have time," that's the sign it needs to move up in
  priority — it's the first thing a lot of testers/demo viewers will
  actually see.

**Scope creep into feed architecture**
- *Problem:* trying to build a real fan-out system (write-time precompute,
  cache invalidation, etc.) inside a single class session — an infinite time
  sink for a feature that just needs to *look* correct in a demo.
- *Signal you're doing it right:* if a team member starts talking about
  caching layers, message queues, or write-time fan-out, that's the signal
  to stop and confirm you actually need it for a seeded mock dataset at demo
  scale. You almost certainly don't — fan-out-on-read against mock data is
  enough, and time spent beyond that is time not spent on the other two
  features.

**Persistence over-engineered**
- *Problem:* building account-level, cross-device sync of the toggle state
  when nobody asked for it, instead of the simple session/local version.
- *Signal you're doing it right:* "does it survive a page refresh in this
  demo" is the actual bar for the class build. If you're designing a
  database column and a settings-sync endpoint for this, scale it back.

## Visual References

**Precedent apps (for internal team reference, not to be copied verbatim —
screenshots reviewed during research, not reproduced here for IP reasons):**
- X/Twitter home — "For you" / "Following" as two persistent tabs directly
  under the header, active tab underlined. This is the placement pattern to
  copy.
- Instagram — the chronological "Following" feed exists but sits behind a
  small icon in a corner dropdown, several taps deep. This is the placement
  pattern to avoid.
- Threads — same tab pattern as X, positioned at the very top of the home
  feed, persists as you scroll.

**Wireframe of the three states this feature actually needs** (sketched
during spec review, not final visual design):

1. **Default toggle state** — two-tab segmented control ("For you" /
   "Newest") pinned above the feed, active tab underlined, feed content
   directly beneath with no extra chrome in between.
2. **Live-update state** — when new pins arrive while scrolled down in
   "Newest," a small pill ("↑ 3 new pins") appears docked above the feed
   content instead of the list reflowing; tapping it reveals the new pins.
3. **Empty state** — centered icon + one-line headline ("Nothing here yet")
   + one-line body ("Follow some boards to see their newest pins"), shown
   in place of the feed when the user follows nobody or nobody's posted yet.

## Open Questions

- Does the toggle preference sync across devices for logged-in users, or is
  it local-only? (Not required for the class build — flag for later.)
- Should "Newest" show reposts/re-pins from followed accounts, or only
  original pins?
