# Application Hardening Plan

Goal: take the three features from "works in a demo with clean seed data" to "doesn't visibly break under real-world messiness" — bad input, missing data, concurrent edits, small screens, keyboard-only users, and a shared `localStorage` store three features are all reading and writing.

This plan is split into one phase per feature owner, plus an integration phase. Each phase is scoped to what that person already owns, so hardening happens alongside — not after — their feature work.

## Cross-Cutting Principles (apply in every phase)

These aren't a separate phase — bake them into whichever phase you're doing:

1. **Never trust stored data.** Anything read from `localStorage` (settings, layout preference, notes) may be missing, malformed, or from an older schema version. Every read needs a fallback to a safe default, not a crash.
2. **Never trust user-entered text.** Feature 1's "Add note" is free text rendered back to the screen — this is a real XSS surface if rendered with `innerHTML` or `dangerouslySetInnerHTML` instead of escaped text rendering. Same caution applies to any pin title/tag coming from the "seed dataset" once real data replaces it.
3. **Fail visibly to the developer, fail quietly to the user.** A broken image, a missing field, or a corrupted settings blob should never produce a blank white screen or console-only silent failure — the user sees a graceful fallback (toast, empty state, default value), while `console.error` or similar still logs the real cause for debugging.
4. **Every interactive element needs a non-mouse path.** Keyboard access isn't just Feature 1's problem — it applies to the Feature 2 toggle and Feature 3 layout switcher too.
5. **Test against messy data, not just the seed dataset.** Long titles, missing images, zero-pin boards, users who follow nobody — these are correctness bugs, not polish.

---

## Phase 1 — Martha: Harden Feature 1 (Customizable Quick Actions)

Building on [research/feature-1-customizable-quick-actions/README.md](feature-1-customizable-quick-actions/README.md).

- **Input validation on settings:** enforce min-1/max-4 at the data layer, not just the UI — if `localStorage` is hand-edited or corrupted to hold 0 or 6 actions, the app must self-correct back to a valid state on load, not just prevent it going forward from the UI.
- **Schema versioning on the settings blob:** add a version field to the stored JSON now, even if unused this session — so a future settings-shape change (e.g. adding reordering) doesn't crash on old stored data. Cheap now, expensive to retrofit later.
- **Action execution safety:** every entry in `ACTION_CATALOG.run()` needs a try/catch — e.g. `navigator.share` rejecting, `navigator.clipboard.writeText` failing (unsupported/blocked by permissions), or a broken `imageUrl` on Download — all should toast a clear failure message, never throw unhandled.
- **XSS on Add note:** confirm the note text is rendered as plain text (React's default JSX text interpolation is safe; explicitly avoid any `dangerouslySetInnerHTML` for note content) and confirm `window.prompt` input is stored/displayed safely.
- **Long-press vs. scroll conflict:** stress-test the touch handler with fast scrolling, multi-touch (e.g. accidental two-finger touch), and rapid tap-tap-hold sequences — the timer-cancel-on-movement logic is the single most likely thing to visibly misfire in front of an audience.
- **Keyboard access end-to-end:** verify the full tab → focus-visible → Enter → arrow-navigate → Escape flow works with a real keyboard, not just visually reviewed in code — this is easy to half-implement (e.g. focus trap without the Escape handler).
- **Cross-platform consistency check:** changing settings on one surface (say, resize to mobile width) must be reflected the next time the other surface (desktop three-dot) is used — since they read the same store, this should be automatic, but verify it isn't cached anywhere per-surface.

**Definition of done for Phase 1:** settings can't be forced into an invalid state through any path (UI, direct localStorage edit, or missing key), every action handler fails gracefully, and the full keyboard flow works without a mouse.

---

## Phase 2 — Rhea: Harden Feature 2 (Chronological Feed Toggle)

Building on [research/feature-2-feedtoggle/feature-2-chronological-toggle-spec.md](feature-2-feedtoggle/feature-2-chronological-toggle-spec.md).

- **Empty state is a first-class state, not an afterthought** — per the spec's own "Problem Spots" section, build and test this before the populated state, not after.
- **Ordering stability under stress:** test the `created_at` + ID tiebreak sort against a dataset with deliberately duplicated timestamps (not just naturally-varied seed data) to confirm no duplicate/skipped pins appear as new content streams in.
- **"New pins" pill correctness:** verify the pill's count is accurate (not off-by-one) and that tapping it inserts pins in the correct position without disturbing scroll position of content already on screen.
- **Toggle state persistence integrity:** if the stored toggle preference is corrupted/missing, default to "For You" (per spec) rather than crashing or defaulting to an undefined state — same "never trust stored data" principle as Phase 1, applied to a different key in the shared store.
- **No accidental ranking creep:** as a hardening/code-review step, confirm the "Newest" query path has zero scoring or relevance logic anywhere — this is a correctness guarantee as much as a hardening one, per the spec's explicit warning that this is the easiest place for scope creep to quietly break the feature's entire premise.
- **Keyboard/focus on the tab control:** the segmented "For You"/"Newest" control must be operable via keyboard (native `<button>`/`role="tab"` semantics, not click-only `<div>`s), consistent with Phase 1's keyboard bar.
- **Follow-list edge cases:** a user who follows exactly one account, an account that's followed but has zero pins, and an account that's unfollowed mid-session while "Newest" is open — all three need explicit testing, not just the "follows several active accounts" happy path.

**Definition of done for Phase 2:** the empty state is designed and tested, ordering is stable under duplicate timestamps, and the toggle can't end up stuck in an invalid or crashed state regardless of what's in storage.

---

## Phase 3 — Frances: Harden Feature 3 (Board-View Layout Switcher)

Building on [research/feature-3-board-layout-switcher/README.md](feature-3-board-layout-switcher/README.md) and the shell's own TODOs.

- **Close out the shell's flagged gaps first** — these are hardening work, not new scope: the sparse-row fix for 0–2 pin boards in `GridView.tsx`, 2-line title truncation in `ListView.tsx`, and scroll-position preservation on layout switch (explicitly called out in the shell as required, not optional).
- **Resolve the missing spec file gap:** the shell references `feature-3-board-layout-switcher-spec.md` as its acceptance-criteria source of truth, but that file isn't in the repo — track it down or reconstruct its contents before treating any TODO as "done," since there may be criteria beyond what's visible in the shell's own README.
- **Shared type/storage coordination with Phase 1 and Phase 2:** `types.ts`'s `Pin` interface already anticipates `createdAt` and `following` fields used by Feature 2 — confirm this is the *actual* shared type (not a second copy) once Feature 1 and Feature 2's real data models exist, and confirm `boardLayoutStore.ts`'s `STORAGE_KEY` doesn't collide with Feature 1's settings key in the shared `localStorage` blob.
- **Responsive breakpoint verification:** actually test the 600px Grid breakpoint (2 per row) and the very-small-width fallback (1 per row) on a real narrow viewport, not just written as a CSS rule — breakpoints are easy to write and easy to leave untested.
- **Per-board isolation:** verify that setting Board A's layout to List doesn't leak into Board B's stored preference — since this is keyed by `boardId`, a bug here would likely show up as "every board shows the same layout," which is an easy mistake with a shared store.
- **Malformed/missing pin data in each view:** a pin with no image (Grid/Masonry), no title (List), or an extremely long title (List truncation) — test all three against every layout mode, not just the mode where the issue is most obvious.

**Definition of done for Phase 3:** every TODO in the shell is either resolved or explicitly deferred with a reason, the missing spec file question is resolved, and layout preference is verified as per-board rather than assumed to be.

---

## Phase 4 — Steve: Integration & Final Hardening Pass

This phase starts once Phases 1–3 have working code, not after — Steve should be reviewing incoming pieces throughout, not just at the end.

- **Unify the shared `localStorage` schema.** Features 1 and 3 (and Feature 2's toggle state) all persist to local storage. Confirm they're using one coordinated JSON shape/key structure — per the cross-feature decision already on record in [research/features.md](features.md) — rather than three independent, possibly colliding keys. This is the single highest-risk integration point, since all three phases were built somewhat in parallel.
- **Reconcile the `Pin` type across all three features into one shared `types.ts`**, rather than three near-identical copies — Feature 3's placeholder already anticipates this; make it real.
- **Wire the actual data flow end to end:** confirm the board page passes real `boardId`/`pins` props into Feature 3's `BoardLayoutSwitcher`, the feed page passes real follow-state into Feature 2's toggle, and Feature 1's settings actually drive both the desktop and mobile surfaces — integration bugs live in these seams, not inside any one feature's own code.
- **Cross-feature interaction testing:** e.g., does switching Feature 3's board layout to List still allow Feature 1's quick actions to work correctly on each row? Does Feature 2's "Newest" toggle interact correctly with whatever layout Feature 3 has set for that context? These combinations were never tested in isolation by any one phase.
- **Full keyboard-only pass across the whole assembled app**, not just within each feature — tab order across all three features together, not just within each one's own component.
- **Full regression pass against the edge-case lists in all three feature docs**, now against the integrated app rather than each feature in isolation — a fix in one feature's phase can silently break an edge case another phase already solved.
- **One final empty/error/loading-state pass** across the assembled app: what does the app look like on first load before any settings exist anywhere? That's the actual demo-day cold-start state, and it's the one state none of the three phases individually own.

**Definition of done for Phase 4(and the whole plan):** all three features work together against one shared, coordinated data/settings layer, the full keyboard-only path works across the entire assembled app, and the app survives a cold start with zero prior state.

---

## Suggested Sequencing

Phases 1–3 can run in parallel, since they're scoped to each person's own feature. Phase 4 should start as a **rolling review**, not a final step — Steve pulling in and sanity-checking each phase's hardening work as it lands, rather than waiting for all three to finish before touching anything. This avoids discovering integration-breaking assumptions (like a `localStorage` key collision) only at the very end, which is exactly the kind of gap this whole plan exists to prevent.
