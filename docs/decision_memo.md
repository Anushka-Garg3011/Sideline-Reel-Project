# SidelineReel: What We Build Next

**Author:** Anushka Garg, PM · **Decision needed from:** Product lead + VP Sales (Jamal Whitfield), before we reply to Ridgeline

## Decision in one line
**P1 is roster-tagging accuracy:** stop wrong-kid highlights when jersey numbers look alike (14/4, 1/11, swapped digits). We'll also run a cheap A/B test that moves reel notifications out of late-night hours. We are **not** building livestreaming.

## How we scored it (RICE)
The signals give no absolute reach or effort numbers. **Reach (% of active families per quarter) and Effort (person-months) are my assumptions**, to be swapped for real figures from analytics and engineering. The ranking holds when those change, except for the notification fix.

| # | Candidate | Reach | Impact | Conf. | Effort | RICE | Core gate |
|---|---|---|---|---|---|---|---|
| A | Notification timing (stop late-night sends) | 40 | 0.5 | 50% | 0.25 | **40** | Core |
| B | Roster-tagging accuracy (wrong-kid highlights) | 30 | 3 | 70% | 2 | **31.5** | Core |
| C | Share link for grandparents and scouts (view-only) | 25 | 1 | 50% | 1.5 | **8.3** | Adjacent |
| D | Watermark missing on camera-roll save | 5 | 0.5 | 50% | 0.25 | **5.0** | Core (minor) |
| E | App icon for all-sports leagues | 3 | 0.25 | 80% | 0.25 | **2.4** | Off-core |
| F | Livestreaming | 15 | 2 | 30% | 12 | **0.75** | Off-core |
| G | Reel Editor improvements | 10 | 1 | 20% | 3 | **0.67** | Core, weak evidence |

### Reasoning behind the key scores
- **B, tagging:** Three tickets from three different clubs (Ridgeline, Brightwater, Cobblestone) describe the same failure with look-alike numbers. That consistency is why confidence is 70% on only three tickets. Tickets under-report, and analytics links wrong-kid thumbnails to the ~30% of reels never opened. Impact is 3 because a wrong-kid highlight breaks the product's core promise to the very kids and parents it's meant to delight.
- **A, notifications:** It scores highest because it's cheap, but confidence is only 50%. Analytics says "most" unopened reels are due to timing or thumbnails, which is a hypothesis, not a measurement. So it's an A/B test, not a project.
- **G, editor:** The survey says 58% want more editing. Behavior says 6% open the editor and under 1% of those finish an edit. I trust behavior on what people do. The survey likely captured a wish list from people who haven't tried the tool. Confidence stays at 20% until we know why people abandon it.
- **F, livestreaming:** Off-core, at least 12 person-months by my assumption, and its audience (grandparents, scouts) isn't a current user. The $180k is real but isn't a RICE factor, so I treat it as an overlay (below).

### Overrides that change the order
- **Trust and safety:** showing a child in the wrong kid's reel is a kids'-footage issue, so B moves to P1 regardless of score. The order would hold anyway: B is the bigger, root-cause problem, and A is a cheap fix that ships alongside it.
- **Revenue overlay:** the Ridgeline parent's ticket is part of B's evidence, so fixing B addresses Ridgeline's actual experience of the product before signups.
- **Fix before add:** the broken core flow beats every new feature.

## Final roadmap
### Build now
1. **P1: Roster-tagging accuracy.** Spec and prototype attached.
2. **Ship alongside as a quick test:** move reel notifications out of late-night hours (A/B test).

### Test or investigate
3. **Share link for non-roster viewers (C).** This is the in-core answer to what Ridgeline's board wants: family who can't attend can watch without being on the roster. Families already share. Validate demand with Ridgeline first.
4. **Reel Editor (G).** Run 5 parent interviews and a funnel check to see where people drop off. Don't build more controls yet.

### Backlog
5. Watermark bug (D) and app icon (E). The icon is a design-team call.

### Not building
- **Livestreaming (F).** It's off-core, the highest effort on the list, and rests on one account's request. Building it would delay the fix for wrong-kid reels, which is what's damaging trust.

## Two metrics to correct
1. **Reel Share Rate (92%)** counts only reels a parent opened. About 30% are never opened, so share rate across all generated reels is closer to **~64% (92% × 70%)**, assuming unopened reels aren't shared. Replace it with **reel open rate** and **wrong-kid report rate**.
2. **"Share used in 90% of recaps sent"** looks high next to a 30% unopened rate. Ask analytics for the exact definition before using it as evidence for C.

## What to tell Ridgeline
Carla's renewal and signups open in about six weeks. **Don't promise livestreaming.** Offer a call (PM + Jamal Whitfield) covering:
- **The tagging fix**, including the issue her own parent reported, with a date once engineering confirms it.
- **A view-only share link** for grandparents and scouts, as the in-product answer to "watch without being on the roster".
- **Livestreaming** as something we're exploring with a partner, not a commitment.

**No dates go to Ridgeline until engineering has estimated them.**

## Next steps
| Owner | Action | When |
|---|---|---|
| Engineering | Size roster-tagging accuracy and the notification test | This week |
| PM + Jamal Whitfield | Call Carla with the message above (dates only after sizing) | After sizing |
| Analytics | Replace Reel Share Rate; define "Share used in 90% of recaps"; supply real reach figures | This week |
| Analytics | Share the wrong-kid report rate ("Not my kid" reports per 1,000 published clips) with Product, Engineering and Sales, broken down by club and jersey-number pair | Baseline after the shadow week, then weekly |
| PM | 5 parent interviews + editor funnel check | Next 2 weeks |
| PM | Validate share-link demand with Ridgeline | Next 2 weeks |
