# Feature 1 Deep Dive: Customizable Quick Actions

This folder is the working spec for feature 1, broken out from [../features.md](../features.md) because it's the feature with the most open questions — the original written spec and the team's POC code disagreed on what a "quick action" even is.

## Contents

- [action-inventory.md](action-inventory.md) — reconciles the spec's action list against the POC's actual code; there's a real mismatch here, not just a naming difference
- [spec.md](spec.md) — the settled behavior spec (desktop vs. mobile, defaults, settings)
- [implementation-plan.md](implementation-plan.md) — concrete build steps and what to reuse from the POC vs. build new
- [edge-cases.md](edge-cases.md) — things that will break the demo if untested

## TL;DR of the open item

The written spec listed 9 actions. The POC's code (`action-catalog.ts`, `types.ts`) implements a different set of 10. Only 6 actions are common to both. The POC also has a `slots: 3 | 4 | 5` system and three named presets (creator/collector/social) that were never in the written spec at all. **This needs a team decision before building** — see [action-inventory.md](action-inventory.md) for the exact reconciliation and a recommended resolution.
