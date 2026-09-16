# Feature 3 Deep Dive: Board-View Layout Switcher

Pain point and original spec are in [../features.md](../features.md#3-board-view-layout-switcher). This folder holds the team's build-ready scaffold for it.

## Contents

- `board-layout-switcher.zip` — a teammate's code scaffold (uploaded 2026-09-15), containing:
  - `BoardLayoutSwitcher.tsx` — the toggle + view-switching container
  - `boardLayoutStore.ts` — localStorage read/write helpers for the layout preference
  - `types.ts` — a placeholder `Pin` type
  - `views/MasonryView.tsx`, `views/GridView.tsx`, `views/ListView.tsx` — one file per layout mode

## Status: scaffold, not finished

The zip includes its own `README-feature-3-shell.md` (worth reading in full — extract the zip to see it) written by the teammate who built it, addressed to "Steve." Key points from it:

- This is explicitly **not** a finished feature — every `TODO (Steve)` marker is an intentional gap, not an oversight.
- **`MasonryView.tsx` is a stub** — it should be replaced with the board page's *existing* masonry rendering relocated into this file, not rewritten from scratch.
- **`GridView.tsx`** still needs the sparse-row fix (0–2 pins on a board) and the 600px mobile breakpoint — both already called out as required edge cases in [../features.md](../features.md).
- **`ListView.tsx`** still needs 2-line title truncation CSS.
- **Scroll-position preservation on layout switch is not implemented** — flagged with a TODO in `BoardLayoutSwitcher.tsx`, and the teammate's note says this is a required acceptance criterion per their spec, not optional.
- **`types.ts`'s `Pin` type is a guess**, not authoritative — check whether a `Pin` type already exists elsewhere in the repo (e.g. from feature 1's `types.ts`, once that's built) and import that instead of duplicating it.
- **Coordinate `STORAGE_KEY` and the settings blob shape with whoever builds feature 1** — both features persist to `localStorage`, and per the cross-feature decision in `features.md`, they should share one preferences store rather than each inventing its own key/shape.

## Gap: referenced spec file is missing

The shell's own README references `feature-3-board-layout-switcher-spec.md` as the source of truth for scope and acceptance criteria (including the scroll-position requirement), but that file isn't in the zip and doesn't exist elsewhere in this repo. **Someone needs to track that down or reconstruct it** — the shell was clearly built against a spec none of the rest of the team has seen yet.

## Before merging (from the shell's own checklist)

1. Confirm whether a `Pin` type already exists elsewhere in the repo before keeping the placeholder in `types.ts`.
2. Confirm where the board page currently lives and what it passes for pins — `BoardLayoutSwitcher` expects `boardId: string` and `pins: Pin[]` as props.
3. Implement scroll-position preservation on layout switch — explicitly called out as required, not optional.

## Confirmed out of scope (per the shell)

- No global layout setting — per-board only (matches the resolved decision already in `features.md`)
- No drag-to-reorder
- No custom Grid column counts beyond 4 (desktop) / 2 (mobile), or 1 at very small widths if needed
