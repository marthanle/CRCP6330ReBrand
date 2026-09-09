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

## Repo Contents

- [`research/pinterest-research.md`](research/pinterest-research.md) — pain points, existing features, rebrand concept, precedent research, sourced links
- [`research/features.md`](research/features.md) — the 3 confirmed feature specs, including the desktop-vs-mobile interaction split for feature 1

## Status

Research phase complete. A prior team POC (Next.js app with a working desktop quick-actions bar, `QuickBar.tsx`) was shared and then removed from the repo — its `action-catalog.ts` already covers a superset of feature 1's action list. Building not yet started; that POC should be revisited as a starting point rather than a rewrite once the team resumes.
