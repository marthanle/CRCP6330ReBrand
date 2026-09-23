# CRCP6330ReBrand

## Challenge

Work in teams to come up with a creative, aesthetic, and functional re-brand of an existing web-based tool (e.g. Canvas, WhatsApp, Instagram, TikTok, Facebook, Etsy, eBay). Pick one, decide what you'd improve and why, and scope a plan that a team of 3–4 can feasibly build in a single class session.

**Chosen platform: Pinterest**

## Rebrand Concept: "Sourced"

Pinterest for people burned by AI slop and dead links — same save-and-plan behavior, but every pin is traceable, verifiable, and under the user's control (not just the algorithm's).

Full background research — current Pinterest pain points, existing features to preserve, and sourcing — is in [research/pinterest-research.md](research/pinterest-research.md).

## Confirmed Feature Updates

Full specs and implementation notes for each are in [research/features.md](research/features.md).

1. **Customizable quick actions** — defaults to Pinterest's current 4 (See less, See more, Share, Save), customizable from a settings screen using actions already available elsewhere in the app (e.g. Download image, Hide, Report, Copy link). Two interaction surfaces share one config: a hover-reveal action bar on desktop, and a long-press menu on mobile (desktop web has no long-press gesture).
2. **Chronological feed toggle** — a "For You" / "Newest" switch on the home feed, so users can escape the algorithmic feed and see strictly newest-first content from who they follow.
3. **Board-view layout switcher** — per-board toggle between Masonry (today's default), Grid (uniform square crops), and List (thumbnail + title + tags), since one layout doesn't fit all board types (outfits vs. room comparisons vs. recipes).

## Team & Ownership

- **Martha Le** — deep dive on Feature 1 (Customizable Quick Actions): [`research/feature-1-customizable-quick-actions/`](research/feature-1-customizable-quick-actions/)
- **Rhea** — deep dive on Feature 2 (Chronological Feed Toggle): [`research/feature-2-feedtoggle/`](research/feature-2-feedtoggle/)
- **Frances Parker** — deep dive on Feature 3 (Board-View Layout Switcher): [`research/feature-3-board-layout-switcher/`](research/feature-3-board-layout-switcher/)
- **Steve Elias Oregel** — building the combined mockup, bringing all three features together into one app

## Working Prototype

[`prototype/sourced-prototype.html`](prototype/sourced-prototype.html) is a single self-contained HTML/JS file with no build step and no network calls — all 3 features are live and testable:

- Customizable quick actions (Settings → Edit → Checklist → Save, min 1 / max 4, desktop hover-reveal three-dot menu + mobile long-press)
- "For you" / "Newest" chronological feed toggle
- Per-board Masonry / Grid / List layout switcher, with real generated-image board covers

Visually restyled to match the real Pinterest app (white canvas, red accent, rounded borderless pin cards, Pinterest-style top bar with search/notification icons, mobile bottom tab bar) rather than the earlier "herbarium specimen label" concept art direction.

## Pitch Deck

[`pitch-deck/sourced-pitch-deck.pptx`](pitch-deck/sourced-pitch-deck.pptx) — an 11-slide deck making the case for this as an initiative: why Pinterest, the pain points, the "Sourced" concept, all 3 features with precedent, the working prototype as proof, and the case for moving forward. Slide-by-slide breakdown in [`pitch-deck/README.md`](pitch-deck/README.md).

## Hardening Plan

[`research/hardening-plan.md`](research/hardening-plan.md) — a 4-phase plan (one phase per feature owner, plus a rolling integration phase) for taking the app from "works in a demo" to handling messy real-world data, invalid stored settings, and keyboard-only use.

## Repo Contents

- [`research/pinterest-research.md`](research/pinterest-research.md) — pain points, existing features, rebrand concept, precedent research, sourced links
- [`research/features.md`](research/features.md) — the 3 confirmed feature specs, including the desktop-vs-mobile interaction split for feature 1
- [`research/feature-1-customizable-quick-actions/`](research/feature-1-customizable-quick-actions/) — Martha's deep dive: full action list, settings flow, platform split, edge cases
- [`research/feature-2-feedtoggle/`](research/feature-2-feedtoggle/) — Rhea's deep dive on the chronological toggle
- [`research/feature-3-board-layout-switcher/`](research/feature-3-board-layout-switcher/) — Frances's deep dive and code scaffold for the layout switcher
- [`research/hardening-plan.md`](research/hardening-plan.md) — the 4-phase hardening plan
- [`prototype/sourced-prototype.html`](prototype/sourced-prototype.html) — the working, Pinterest-styled prototype with all 3 features live
- [`pitch-deck/`](pitch-deck/) — the pitch deck and its slide-by-slide description

## Status

All 3 features are specced, deep-dived, **and built** in a working prototype — not just concepts. The pitch deck and hardening plan are also done. Next up: working through the hardening plan's 4 phases, and using the pitch deck to make the case for this becoming a real initiative rather than a one-off class project.

A prior team POC (Next.js app with a working desktop quick-actions bar, `QuickBar.tsx`) was shared and then removed from the repo — its `action-catalog.ts` covered a superset of feature 1's action list and informed the prototype's implementation.
