# Action Inventory: Spec vs. POC Reconciliation

## The mismatch

| Source | Action count | Actions |
|---|---|---|
| Written spec ([features.md](../features.md)) | 9 | See less, See more, Share, Save, Download image, Hide pin, Report pin, Copy link, Send to board directly |
| POC code (`action-catalog.ts` / `types.ts`) | 10 | Save, Download image, Share, Copy link, React, Send to friend, Hide pin, Report, Add note, Find similar |

## Overlap breakdown

**Shared (6) — safe, no decision needed:**
- Save
- Share
- Download image
- Hide pin
- Report (pin)
- Copy link

**In the spec, missing from the POC entirely (3) — someone has to build these or drop them:**
- See less
- See more
- Send to board directly (note: POC's "Send" is *Send to friend*, not this — a different action with a similar name, don't conflate them)

**In the POC, not in the original spec (4) — someone has to decide if these stay:**
- React (❤️ reacts to a pin)
- Send to friend
- Add note (per-pin note, stored per pin ID in `localStorage`)
- Find similar

## Also undocumented in the written spec: the slots + presets system

The POC's `types.ts` defines:
```ts
export type SlotCount = 3 | 4 | 5;

export type QuickActionSettings = {
  slots: SlotCount;
  order: ActionId[];
  updatedAt: string;
};
```

This means the POC isn't just "pick which actions show" — it's "pick how many slots are visible on the bar (3, 4, or 5) AND which actions fill them," with everything else falling into the ellipsis/overflow menu. The written spec never mentions a slot count at all; it implies a simple show/hide checkbox list.

The POC also ships three named presets, pre-filling different combos:

| Preset | Slots | Order |
|---|---|---|
| `creator` | 4 | Save, Download, Add note, Find similar |
| `collector` | 3 | Save, Download, Copy link |
| `social` | 5 | Save, Share, Send to friend, React, Copy link |

None of these presets are mentioned anywhere in the written spec.

## Recommended resolution (needs team sign-off, not yet decided)

1. **Adopt the POC's 6 shared actions as the confirmed baseline** — no argument needed there.
2. **Drop "See less" / "See more" from the quick-action scope.** They're view-feedback actions ("show me less/more of this"), arguably closer to feed-tuning than a quick action on a single pin, and the POC team already didn't build them — treat that as a deliberate simplification, not an oversight, unless someone objects.
3. **Rename/clarify "Send to board directly" vs. POC's "Send to friend."** These are different features wearing similar names. Decide: do we want *both* (send-to-board AND send-to-friend), just one, or neither? This is a scope call, not a bug.
4. **Decide on Add note and Find similar.** These are the most "extra" of the POC's additions — Add note is a real, working stub (writes to localStorage); Find similar is a stub toast only. Recommend keeping Add note (it's already functional and demo-worthy) and cutting Find similar (it does nothing and adds scope for no visible payoff) — but this is the team's call.
5. **Adopt the slots (3/4/5) + presets system as-is.** It's already built, already tested by the POC author, and makes the settings screen feel more finished (three one-click presets vs. spec's plain checkbox list) — this should be treated as an upgrade to the spec, not scope creep, since it's zero new work.

## Finalized action list (pending team approval)

If the recommendation above is accepted, the confirmed action set becomes:

1. Save
2. Share
3. Download image
4. Hide pin
5. Report
6. Copy link
7. React
8. Add note

(8 actions, dropping See less/See more/Find similar, deferring the send-to-board vs. send-to-friend decision.)
