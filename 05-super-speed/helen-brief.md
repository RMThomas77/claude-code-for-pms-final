# Brief for Helen: Vesper and the quiet-responder fix

Draft v5, 9 Oct 2026. Brief only, nothing built. Not yet reviewed with Wen Li or Marcus.

## The problem, through Vesper
Vesper covers Old Town. Before 4.2 they were pinged on 58 of 69 callouts. After, 13 of 41, first on only 8. About 12 pings a week fell to about 1, and their last taken ping was 17 Aug. Demand didn't dry up; the pings went to Nightwell, Captain Vantage and The Longcast. Three other responders show the same pattern. Their pings are mostly missed (53-64%), but their turn-down rate hasn't moved, so they haven't stopped saying yes.

**Likely cause (not proven):** the 60s wait means more misses, and a miss lowers a score exactly like a refusal. The heavier proximity weight then pushes them down the list. Only taking a ping raises a score, so there is no way back. Wen's 2019 TODO in `history.py` asked whether scores should ease back; Vesper is the case it never covered.

## What we should fix
Make a score reflect whether someone says no, not whether they were reached. In order:
1. **A missed ping stops counting as a refusal.** Today it costs the same 0.12 as a turn-down. Score misses separately, lightly or not at all.
2. **A score that sinks because someone wasn't offered work eases back toward neutral.** This answers Wen's 2019 question. A responder who keeps refusing still stays down; one who simply stopped being pinged, like Vesper, gets back in the rotation.

Two details matter, both from testing in the prototype:
- **Recovery has to be quick.** At +0.05 a week it takes about 8 weeks to get Vesper back to neutral (0.5). At +0.15 it takes about 3, and +0.25 about 2. Propose +0.15 to start; engineering to tune.
- **Responders already sunk need a one-off reset to neutral.** Vesper and the other three are stuck now. A reset (per responder, so it can be targeted) puts Vesper back in 3rd straight away; waiting for the drift takes weeks. This is a one-time data fix, not a new feature.

Both fixes are small engineering changes. Don't change the wait or weights yet; the dev look below decides that. The follow-ups at the end come after the fixes.

## Who it's for
Vesper, a responder in good standing who has been quietly dropped. Handlers like Kip benefit second-hand: work spreads evenly again without them doing anything.

## What changes once it exists
| | Today | After |
|---|---|---|
| **Vesper** | About 1 ping a week, nothing taken since 17 Aug, no way to recover. | Reset to neutral now, so back in rotation within a callout or two; a miss no longer counts as a refusal; the score keeps climbing by taking work and recovers in weeks, not months. |
| **Kip** | Sees uneven work and "gone before they could answer". | Work spreads back across Old Town responders. No console change yet. |

## What it deliberately doesn't do
- Change a number quietly and call it handled.
- Promise Vesper work: a responder who keeps missing or refusing still ranks lower.
- Change the console: no quiet-responder flag yet, and handlers still can't change routing.
- Link a cover name to a person (Security Policy 4.1); it runs on ping history only.
- Settle 90s vs 60s, or add Availability Confidence or mutual aid.

## Separate: did 90s to 60s help? (dev to examine)
It didn't speed up pickup. A miss now costs 62s instead of 92s, but misses per callout rose from about 0.025 to about 0.20. Callouts with nothing taken rose from 5.5% to 11.1%, pings per callout from 1.23 to 1.39, and average seconds to the taken ping from 7.2 to 17.5. `pings` has no answer-time field, and the weights changed the same day. **Dev ask:** pull real answer times and replay post-4.2 callouts on 90s and the old weights separately. Also find out why the wait was lowered; Priya left no reason.

## Not known
Whether neutral is enough. In the prototype, recovery to 0.5 plus a reset lifts Vesper to 3rd but not past the others, who keep earning credit; the 0.60 weight on proximity limits how far an acceptance score can carry them. Prototype values for proximity and starting scores are my assumptions, so ask Wen or Marcus for real travel times. Whether 4.2 reset scores (held in memory, so a restart may have wiped them); Vesper's answer and travel times; how Vesper feels (no responder interviews; follow-up 4); prior-year baselines for "August is always soft". Risk to watch: Supply's low-load scheduling (night callouts halved, 13.8 to 6.5 a week).

## Ask
Agree these two fixes, the faster recovery and the one-off reset to neutral before anyone touches code. A clickable prototype is in `prototype.html`. OK for me to put the open questions to Wen and Marcus.

## Follow-ups, after the two fixes
1. **Possibly revert the ping wait (60s back to 90s).** Decided by the dev look above. In the model, a 90s wait stops the fall but can't undo it alone, so it follows the fixes rather than replacing them.
2. **Possibly revert the distance weight (0.60 back toward 0.45).** Only worth doing if real travel times show Vesper is far from the callouts. If they're close, a lower weight hurts them. In the prototype a smaller change barely moved the numbers.
3. **Responder console.** Work with Sofia on a view that flags who has gone quiet and why, so handlers like Kip see it before a ticket.
4. **Survey the responders.** Once the fixes are live, ask Vesper and the other quiet responders, plus a few who weren't affected, whether they feel pinged more often, and compare it with the ping data. Nobody has asked them yet. It must use cover names only and never try to identify anyone (Security Policy 4.1).
