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

## Precedent Research for the 3 Confirmed Features

Full feature specs live in [features.md](features.md). Findings below validate each against existing products.

### 1. Customizable Long-Press Quick Actions
- No mainstream social/creative app has shipped a *user-customizable* long-press/context-action menu with a picker UI — this remains a genuine gap, not a re-implementation of an existing feature ([QuickActionView — GitHub](https://github.com/Commit451/QuickActionView)).
- OS-level precedent shows the pattern is well understood by users: macOS lets people enable/disable Quick Actions and assign keyboard shortcuts via System Settings → Extensions ([MacMost](https://macmost.com/customizing-the-mac-context-menu.html)); Windows 11 supports adding custom shortcuts to the right-click context menu ([Windows Central](https://www.windowscentral.com/software-apps/windows-11/how-to-integrate-custom-context-menu-shortcuts-on-windows-11), [Tom's Hardware](https://www.tomshardware.com/software/windows/how-to-add-custom-shortcuts-to-the-windows-11-or-10-context-menu)); iOS Home Screen quick actions work as long-press shortcut menus per app ([Better Programming — iOS 13 Quick Actions](https://betterprogramming.pub/handling-ios-13-quick-actions-67f9e304dcc6)).
- Takeaway: users already have a mental model for "long-press/right-click menu is configurable" from their OS — porting that expectation into Pinterest is a low-learning-curve win.

### 2. Chronological Feed Toggle
- X (Twitter) already ships this exact pattern: a "For You" / "Following" tab toggle, where Following shows a reverse-chronological timeline of accounts you follow ([$99 Social](https://www.99dollarsocial.com/blog/benefits-of-twitters-chronological-timeline), [MakeUseOf](https://www.makeuseof.com/tag/switch-chronological-twitter-timeline/)).
- Instagram tested/shipped three feed-sorting options — Home (algorithmic), Favorites, and Following — with the latter two chronological ([PhoneArena](https://www.phonearena.com/news/instagram-chronological-feed-options_id137603), [TechRadar](https://www.techradar.com/news/instagram-is-testing-an-option-to-show-the-latest-posts-first)).
- Caveat worth noting to the team: X recently started algorithmically re-ranking even its "Following" feed by predicted engagement, requiring a further "Switch to Latest" option to get true chronological order ([Social Media Today](https://www.socialmediatoday.com/news/x-formerly-twitter-sorts-following-feed-algorithm-ai-grok/806617/), [PiunikaWeb](https://piunikaweb.com/2026/02/15/x-following-feed-not-in-chronological-order-heres-what-we-know/)) — a reminder to keep our "Newest" mode strictly chronological with no algorithmic re-ranking, since that erosion is exactly the pain point we're fixing.
- Takeaway: this is a validated, well-understood pattern with two major platforms as direct precedent — low risk, high user-recognition.

### 3. Board-View Layout Switcher
- Notion's database view switcher lets users change how the same underlying items render (table, board, list, gallery, calendar, etc.) without changing the data itself — switching views is purely presentational ([Notion Help](https://www.notion.com/help/views-filters-and-sorts), [Super.so — All Notion Database Views](https://super.so/blog/notion-database-views)); Notion's Board and Gallery views even expose a "Card size" layout control ([Notion Help — Boards](https://www.notion.com/help/boards)).
- Airtable ships six interchangeable views (Grid, Gallery, Kanban, Calendar, Timeline, Form) over the same records, with Gallery view offering a "Customize cards" control for which fields show ([Airtable Support](https://support.airtable.com/docs/getting-started-with-airtable-gallery-views), [Zapier — Airtable Views](https://zapier.com/blog/airtable-views/)).
- Takeaway: "same data, switchable layout" is an established, low-risk UX pattern in productivity tools — our masonry/grid/list switcher applies that same idea to a visual-discovery context where Pinterest currently offers none of it.

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
- [QuickActionView — GitHub](https://github.com/Commit451/QuickActionView)
- [Customizing the Mac Context Menu — MacMost](https://macmost.com/customizing-the-mac-context-menu.html)
- [How to integrate custom context menu shortcuts on Windows 11 — Windows Central](https://www.windowscentral.com/software-apps/windows-11/how-to-integrate-custom-context-menu-shortcuts-on-windows-11)
- [How to Add Custom Shortcuts to the Windows Context Menu — Tom's Hardware](https://www.tomshardware.com/software/windows/how-to-add-custom-shortcuts-to-the-windows-11-or-10-context-menu)
- [Handling iOS 13 Quick Actions — Better Programming](https://betterprogramming.pub/handling-ios-13-quick-actions-67f9e304dcc6)
- [How to Use X's Chronological Timeline in 2026 — $99 Social](https://www.99dollarsocial.com/blog/benefits-of-twitters-chronological-timeline)
- [How to Switch to a Chronological X (Twitter) Timeline — MakeUseOf](https://www.makeuseof.com/tag/switch-chronological-twitter-timeline/)
- [Instagram announces three new feed options — PhoneArena](https://www.phonearena.com/news/instagram-chronological-feed-options_id137603)
- [Instagram is testing an option to show the latest posts first — TechRadar](https://www.techradar.com/news/instagram-is-testing-an-option-to-show-the-latest-posts-first)
- [X Now Algorithmically Ranks Posts in Following Feed — Social Media Today](https://www.socialmediatoday.com/news/x-formerly-twitter-sorts-following-feed-algorithm-ai-grok/806617/)
- [X Following feed not in chronological order — PiunikaWeb](https://piunikaweb.com/2026/02/15/x-following-feed-not-in-chronological-order-heres-what-we-know/)
- [Database views, filters, sorts & groups — Notion Help](https://www.notion.com/help/views-filters-and-sorts)
- [All Notion Database Views Explained — Super.so](https://super.so/blog/notion-database-views)
- [Board view (Kanban) in Notion — Notion Help](https://www.notion.com/help/boards)
- [Getting started with Airtable Gallery Views — Airtable Support](https://support.airtable.com/docs/getting-started-with-airtable-gallery-views)
- [How to create and customize Airtable views — Zapier](https://zapier.com/blog/airtable-views/)
