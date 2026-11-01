# Roster-Tagging Accuracy: Product Spec

**Product:** SidelineReel (Ajaia) · **Author:** Anushka Garg, PM · **Status:** Draft for engineering review · **Priority:** P1
**Prototype:** https://6ae76296b4412e9346da4632--sideline-reel-prototype.netlify.app/ · **Walkthrough video:** [link]

## 1. Problem
SidelineReel matches a jersey number in the footage to a name on the roster (roster tagging). When two kids on the same roster wear numbers that look alike (4 and 14, 1 and 11, 12 and 21), the tagger sometimes puts one kid's highlight in the other kid's reel. A parent at Ridgeline wrote: *"My daughter wears number 14. Her highlight reel this week had a clip of a completely different kid wearing number 4. She was so disappointed."*

This breaks the product's one promise: *your kid's moments*. It also puts one child's footage in front of another family. Wrong-kid thumbnails are one of the two named reasons that roughly 30% of generated reels are never opened.

## 2. Evidence
| Source | What it shows | Weight |
|---|---|---|
| Support tickets | 3 reports from 3 clubs in 3 sports (Ridgeline soccer 14→4, Brightwater lacrosse swapped digits twice this season, Cobblestone baseball 1→11). Same failure pattern every time. | Medium-high: consistent, independent, and likely under-reported |
| Analytics note | About 30% of generated reels are never opened. Wrong-kid thumbnails, mostly with look-alike numbers, are one of the two named causes. | Medium: a hypothesis, not a measurement |
| Product health metric | Reel Share Rate 92% counts only opened reels. Across all generated reels it's about 64% (92% × 70%), assuming unopened reels aren't shared. | Shows the problem is hidden by the headline number |
| Ridgeline renewal | $180k/season; the club's own parent filed the 14→4 ticket. Carla says reels drove half of their renewals. | Revenue overlay, not a RICE factor |

RICE score 31.5 (Reach 30% of active families/qtr, Impact 3, Confidence 70%, Effort 2 person-months). Reach and effort are assumptions. It moves to P1 because of the trust-and-safety override: a child shown in the wrong kid's reel is a kids'-footage issue.

**What we don't know yet:** the true wrong-kid rate. → Measure it before launch with a 1-week shadow run and a sampled audit (see section 11).

## 3. Users
| User | What they need | What changes for them |
|---|---|---|
| Parent / family | Every clip in my kid's reel is my kid | Fewer wrong clips, plus a one-tap "Not my kid" button when one gets through |
| Coach | Upload once, no editing work | A short stack of clips to swipe through after each game, only for clips the system isn't sure about. Target: a handful per game, under a minute. |
| Club admin | Families stay happy and renew | Fewer complaints; a wrong-kid rate they can see |
| Player (minor) | *Not a direct user.* Their footage should reach only their own family and their team's recap. | Fewer of their clips land in someone else's reel |

## 4. Outcome and metrics
**Outcome:** the right kid in every reel, without adding meaningful work for coaches.

| Metric | Definition | Baseline | Target |
|---|---|---|---|
| **Primary: wrong-kid report rate** | "Not my kid" reports ÷ clips published, per 1,000 clips, weekly | Unknown. Set during the shadow week. | –70% vs baseline in pilot clubs (assumed) |
| **Secondary: reel open rate** | Reels opened ÷ reels generated (replaces the 92% opened-only share rate on the dashboard) | ~70% | +5 pts in pilot clubs (assumed) |
| Guardrail: coach load | Median clips held for coach check per game | — | ≤ 5 per game (assumed) |
| Guardrail: missed highlights | Held clips that expire without review ÷ held clips | — | ≤ 10% |
| Guardrail: recap on time | Weekly recaps sent at their scheduled time | Current | No change |
| Guardrail: kid coverage | Kids with ≥1 clip in the weekly recap, split by look-alike vs other numbers | Current | Kids with look-alike numbers don't fall behind |

**Not a success metric:** Reel Share Rate (92%). It excludes unopened reels, which is where this problem hides.

## 5. Scope
### In scope (v1)
1. **Look-alike detection per roster.** When a roster is saved or changed, find number pairs that are easy to confuse on camera:
   - one number is the other with a digit added (1/11, 4/14, 7/17);
   - digits swapped (12/21).

   This runs in the background. Coaches never see look-alike pairs or thresholds on the roster screen; they only see the clips it holds.
2. **Stricter bar for look-alike numbers.** A clip publishes automatically only if:
   - tagging confidence is ≥ 80% (assumed), **and**
   - when the number has a look-alike on the roster, confidence is ≥ 95% (assumed).

   Otherwise the clip is **held**.
3. **Held clips stay out of everything.** A held clip is never shown in a reel, the weekly recap, or a thumbnail until a coach confirms it.
4. **Coach check (mobile, swipe cards).** Held clips appear one at a time as a card showing a still frame, **"Tagged as #N Name"**, and the reason it was held. The coach decides with one gesture:
   - **Swipe right: correct.** The clip goes into the tagged kid's reel.
   - **Swipe left: wrong kid.** An overlay opens over a blurred background asking "Who is this?". It lists similar-number teammates first, then the rest of the roster. Picking a name moves the clip to that kid's reel. Cancel returns the card.
   - **Swipe down onto the bin at the bottom centre: not our club's kid.** The clip is removed from every reel and recap.
   - Buttons (✕, bin, ✓) under the card do the same three actions, for accessibility and for coaches who don't swipe.
5. **Parent "Not my kid" button** on every clip in a reel. One tap hides the clip from that kid's reel, sends it to the coach check list, and logs a report.
6. **Recap sends on time.** It goes out with confirmed clips only. Clips the coach confirms later are added to the kid's reel.
7. **Instrumentation** for every metric in section 4.

### Out of scope (non-goals)
- **Face recognition or any biometric ID of children.** We will not identify minors by face. Identity stays jersey number + roster + human confirmation.
- Retraining or replacing the vision model. v1 works with the tagger's existing confidence score.
- Improving who counts as "our team" versus the opponent (separate model work). The swipe-down "not our club's kid" action covers it for now.
- More look-alike rules (6/8, 3/8, 1/7). Next version, if the audit shows they matter.
- Notification timing (shipping separately as an A/B test), share links for non-roster viewers, Reel Editor changes, livestreaming.

## 6. Where a human stays in the loop
- **The coach decides identity whenever the system isn't sure.** The system never guesses between two look-alike kids on its own.
- **A parent report is never auto-reassigned.** When a parent taps "Not my kid", the clip is hidden and goes to the coach. It is **not** moved into the other look-alike kid's reel automatically, because a second wrong guess would send a child's footage to yet another family.
- **Unreviewed clips fail safe.** A held clip the coach never reviews expires after 7 days and is never published. A missed highlight is better than a wrong one.
- **Thresholds are owned by a person.** The PM and the ML lead set the 80% / 95% thresholds, and change them weekly from audit data, not automatically.

## 7. Core flow
1. Coach uploads footage (or the sideline camera records it). SidelineReel tags clips.
2. Clean clips publish to each kid's reel. Ambiguous clips are held.
3. Coach gets one notification: "3 clips need a quick check before reels go out." They swipe through the cards: right if the tag is correct, left to pick the right kid, down if it's not our club's kid (≈10–20 seconds a clip).
4. Confirmed clips go into the right kid's reel. "Not our club" clips are discarded.
5. The weekly recap sends on schedule with confirmed clips only.
6. A parent watching a reel taps "Not my kid" on a wrong clip. It disappears from the reel and goes back to the coach's check list.

## 8. Acceptance criteria
| ID | Given | When | Then |
|---|---|---|---|
| AC1 | Roster has #4 and #14 | A clip is tagged #4 at 91% confidence | Clip is held, absent from #4's reel, and listed in the coach check list with #4 and #14 as choices |
| AC2 | Roster has #4 and no look-alike for it | A clip is tagged #4 at 91% | Clip publishes to #4's reel |
| AC3 | Any roster | A clip is tagged at under 80% | Clip is held, whatever the number |
| AC4 | Roster has #12 and #21 | Roster is saved | 12/21 is listed as a look-alike pair. Adding #11 when #1 exists creates 1/11. |
| AC5 | A held clip tagged #4 | Coach swipes right (or taps ✓) | Clip appears in #4's reel within 1 minute and is logged as coach-confirmed |
| AC5b | A held clip tagged #4 | Coach swipes left (or taps ✕) | An overlay opens over a blurred background, listing #14 under "Similar numbers" and the rest of the roster below. The tagged kid (#4) isn't listed. |
| AC5c | The "Who is this?" overlay is open | Coach picks "#14 Zoe K." | Clip moves to #14's reel within 1 minute, is not in #4's reel, and is logged as coach-corrected |
| AC5d | The "Who is this?" overlay is open | Coach taps Cancel or the blurred background | Overlay closes, the card returns, and the clip stays held |
| AC6 | A held clip | Coach swipes down onto the bin (or taps it) | Clip is removed from all reels and recaps for good, logged as "not our club" |
| AC7 | Recap send time arrives with 2 clips still held | Recap sends | It sends on time, contains no held clip, and no thumbnail uses a held clip |
| AC8 | A published clip in #9's reel | #9's parent taps "Not my kid" | Clip disappears from #9's reel within 5 seconds, is listed in the coach check list as "Reported by a parent", is **not** added to any other kid's reel, and the report is counted |
| AC9 | A held clip | Nobody reviews it for 7 days | Clip expires, is never published, and is counted as "expired unreviewed" |
| AC10 | Coach opens the check screen | — | One card at a time shows a still frame, "Tagged as" name and number, and the reason it was held. Each card is decided with one swipe (or one button), plus one tap in the overlay when the kid is wrong. A short drag that doesn't pass the threshold snaps back and decides nothing. |

## 9. Top two ways this could fail once it ships
| # | Failure | What it looks like | What catches it | What we do |
|---|---|---|---|---|
| 1 | **Coaches don't review held clips.** | The check list piles up, clips expire, and kids with look-alike numbers quietly get fewer highlights than teammates. That's a new unfairness. | Guardrails: expired-unreviewed rate per team, and kid coverage split by look-alike vs other numbers. Alert when a team has unreviewed clips older than 48 hours. | Reminder at a sensible hour. Let an assistant coach or team manager review. Lower the hold threshold if load is above 5 per game. |
| 2 | **The tagger is confidently wrong.** | A digit is hidden by an arm or a fold, so 14 reads as a clean, high-confidence 4. It slips past the 95% bar or hits a look-alike pattern we didn't define (6/8). | Primary metric: "Not my kid" reports per 1,000 clips, broken down by number pair. Weekly sampled audit in pilot clubs: a person checks 50 auto-published look-alike clips. | Raise the look-alike threshold, or add the new pair rule. A pair that keeps failing for one team can be set to "always ask the coach". |

## 10. Risks and dependencies
| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| The tagger doesn't expose a per-clip confidence score today | Unknown | High: Effort grows | Engineering confirms in sizing (open question 1) |
| Coach swipes the wrong way by mistake (fast swiping) | Medium | Medium | "Tagged as" name on every card; Correct / Wrong kid stamps show while dragging; short drags snap back. **Should:** an Undo on the confirmation message for 5 seconds (not in the prototype). Parent "Not my kid" is the backstop. |
| Fewer clips in recaps lowers excitement | Medium | Medium | Kid-coverage guardrail; recap never waits on the coach |

## 11. Rollout
1. **Shadow week (all pilot clubs):** run the rules without holding anything. Measure how many clips *would* be held per game, and audit a sample to set the baseline wrong-kid rate.
2. **Pilot:** Ridgeline, Brightwater, Cobblestone (the three clubs that reported it), behind a flag.
3. **All clubs:** after 2 clean pilot weeks. That means the primary metric is down, coach load is ≤ 5 per game, and expiries are ≤ 10%.
- **Rollback:** turn the flag off if recaps send late or the expiry rate goes over 25%.
- **Dates:** set after engineering sizing. Nothing goes to Ridgeline until then.

## 12. Open questions
| Question | Owner | Due |
|---|---|---|
| Does the tagger output a per-clip confidence today, and how well calibrated is it? | ML lead | Before sizing |
| Who besides the head coach may review clips (assistant coach, team manager)? Does that need a consent or permission change? | PM + club ops | Before pilot |
| Can coach-confirmed clips be used to improve the model, given they are minors' footage and what our data consent covers? | PM + legal | Before pilot |
