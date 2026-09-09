# Pinterest Rebrand — Research Notes

## Challenge Recap
Re-brand an existing web platform (creative + aesthetic + functional) as a team of 3–4, scoped to be feasible within a single class session. Chosen platform: **Pinterest**.

## Current Pinterest Pain Points (2025–2026)

### AI content / feed quality
Users have complained about AI-generated pins flooding feeds; a March 2025 ZDNET piece called it "The Enshittification of Pinterest." Users describe the platform as "plagued with advertisers and misleading AI content."
- Source: [Pinterest Reviews — Capterra](https://www.capterra.com/p/234608/Pinterest/reviews/)

### Account suspension / moderation
Late April 2025 saw a wave of account bans with little explanation from Pinterest, leaving users frustrated as of May 2025. Users report scammers going unaddressed while their own accounts get threatened for unclear reasons.
- Source: [Pinterest Ban Wave Sparks Outrage — Quasa](https://quasa.io/media/pinterest-ban-wave-sparks-outrage-a-reminder-that-your-content-isn-t-truly-yours)

### Broken links / misleading content
Users report the platform can be overwhelming, with links that don't work, making it hard to find what they're looking for.
- Source: [Pinterest Pinners Reviews and Complaints — ComplaintsBoard](https://www.complaintsboard.com/pinterest-b127358)

### Scams / low-quality merchants
Complaints about scam ads and rip-off merchants advertising products that don't match what's delivered.
- Source: [Pinterest Reviews — Trustpilot](https://www.trustpilot.com/review/pinterest.com)

### General platform decline
Users note quality decreasing since the end of 2020.
- Source: [Pinterest — Wikipedia](https://en.wikipedia.org/wiki/Pinterest)

## Current Pinterest Core Features (what to keep)

- **Boards & Pins**: save/organize pins into boards; "Make it yours" for fashion/home decor boards; "More ideas" for related-pin recommendations; "All saves" view.
- **Collage Pins**: tap individual items within a collage to shop.
- **Search & Discovery**: keyword + visual search; AI-powered "Pinterest Assistant" for example-based product search; algorithmic home feed blending follows + recommendations.
- **Shopping**: Product Pins with real-time pricing/availability; "Shop the Look" AI-tagged purchasable items; dedicated Shop tab.
- Sources: [Pinterest Shopping Features — SocialChamp](https://www.socialchamp.com/blog/pinterest-shopping/), [Pinterest UI/UX Review — CreateBytes](https://createbytes.com/insights/pinterest-ui-ux-review-boom-or-bloom)

## Rebrand Concept: "Sourced"

**Positioning:** Pinterest for people burned by AI slop and dead links — same save-and-plan behavior, but every pin is traceable and verifiable.

**Key changes & rationale:**
1. **Link-health badge** on every pin (live / broken / redirected), checked at save time — directly answers the #1 complaint (dead links).
2. **Human vs. AI-generated source tag**, filterable in feed/search — addresses AI slop without banning AI content outright.
3. **Decluttered feed toggle** — pure board view (no algorithmic recs/ads) vs. discover view — restores the "calm planning space" users say they've lost.
4. **Trust score on shop links** (based on link age/report history, not real payments) — addresses scam-seller complaints.

## Feature Idea: Customizable Long-Press Quick Actions

Pinterest's current long-press (hold on a pin) menu shows 4 fixed quick actions: **See less, See more, Share, Save**. These are not user-configurable.

**Proposal:** Let users customize which actions appear in this quick-action menu, choosing from an existing action list rather than inventing new ones. Candidate actions to pull from:
- Save (default, existing)
- Share (default, existing)
- See more / See less (default, existing)
- **Download image** (currently only available buried in Pinterest's overflow/"..." menu, not surfaced here)
- Hide pin
- Report pin
- Copy link
- Send to board directly (skip the save-picker step)

**Why:** Reduces friction for power users whose top actions differ (e.g., people who mainly reference-collect images want Download front-and-center instead of See less/See more).

**Scope note for MVP:** Simple to fake for a demo — a settings panel with checkboxes/reorder for a fixed action list, feeding into the long-press menu's rendered order. No real download/share functionality needed, just wiring the UI to prove the customization concept.

## Scoped MVP (single class session, team of 3–4)

Static/local web app, no real backend or auth, seeded fake dataset (~30 pins: `image, title, source, isAI, linkStatus, trustScore`).

| Feature | Owner | Est. time |
|---|---|---|
| Masonry pin grid + board pages | Person A | 1.5h |
| Save/pin flow + create board + drag between boards | Person B | 1.5h |
| Search/filter bar incl. "Human only" toggle + link-health badges | Person C | 1.5h |
| Visual polish, color/type system, trust-score UI, demo data | Person D | 1.5h |

Tech choice: plain HTML/CSS/JS (fastest to demo) or React if team is already fluent.

## Sources
- [Pinterest Reviews 2026 — Capterra](https://www.capterra.com/p/234608/Pinterest/reviews/)
- [Pinterest Reviews — Trustpilot](https://www.trustpilot.com/review/pinterest.com)
- [Pinterest.com — BBB Complaints](https://www.bbb.org/us/ca/san-francisco/profile/internet-service/pinterestcom-1116-448073/complaints)
- [Pinterest Pinners Reviews and Complaints 2026 — ComplaintsBoard](https://www.complaintsboard.com/pinterest-b127358)
- [Pinterest Ban Wave Sparks Outrage — Quasa](https://quasa.io/media/pinterest-ban-wave-sparks-outrage-a-reminder-that-your-content-isn-t-truly-yours)
- [Pinterest — Wikipedia](https://en.wikipedia.org/wiki/Pinterest)
- [The Pinterest Shopping Features You Should Know in 2026 — SocialChamp](https://www.socialchamp.com/blog/pinterest-shopping/)
- [Pinterest Trends 2026 — Adobe](https://www.adobe.com/express/learn/blog/pinterest-trends)
- [2026 Pinterest news and updates — SocialBee](https://socialbee.com/blog/pinterest-news/)
- [Pinterest algorithm: How it actually works in 2026 — Outfy](https://www.outfy.com/blog/pinterest-algorithm/)
- [What Is Pinterest? Features, Search, Boards, Shopping — DocumentaryTube](https://www.documentarytube.com/blog/what-is-pinterest-discovering-its-features-functionality-and-uses/)
- [Pinterest Shop Guide 2026 — Savings Grove](https://savingsgrove.com/blogs/guides/pinterest-shop-guide)
- [Pinterest UI/UX Review: Design Masterclass — CreateBytes](https://createbytes.com/insights/pinterest-ui-ux-review-boom-or-bloom)
