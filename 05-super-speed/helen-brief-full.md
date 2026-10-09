# Brief for Helen: what happened to Vesper, and what we'd build instead

Draft v2, 9 Oct 2026. Brief only, nothing built. Not yet reviewed with Wen Li or Marcus.

## Vesper, in one paragraph
Vesper covers Old Town. Before 4.2 (12 Aug) they were pinged on nearly every Old Town callout: 58 of 69. After 4.2 they were pinged on 13 of 41, and were first to be pinged on only 8. Their pings fell from about 12 a week to about 1. Their last taken ping was 17 Aug. Callouts in Old Town didn't dry up (down 10-17%, nothing like the drop in Vesper's pings); the pings went to Nightwell, Captain Vantage and The Longcast. Vesper has not said no. They have mostly stopped being asked, and when asked they usually miss it.

Vesper is the clearest case of four (with Meteor Mite, The Undertow and Farlight, in four different areas). Group figures: 53-64% of their post-4.2 pings missed, against about 3% before. Their turn-down rate stayed near 21%, so it isn't a change in how willing they are.

## Why, as best we can tell (likely, not proven)
1. 4.2 cut the ping wait from 90s to 60s, so more of Vesper's pings count as missed.
2. A miss lowers the score by 0.12, the same as a turn-down (`history.py`).
3. 4.2 also raised the weight on proximity from 0.45 to 0.60, which pushed Vesper down the list. They are now asked only after others say no.
4. Only taking a ping raises a score, and Vesper is barely pinged. **There is no way back.**

Wen's 2019 TODO in `history.py` asks whether a score should ease back on its own. She weighed both sides: a bad month shouldn't last into spring, but someone who stopped taking work should stay down until they take work again. Vesper is the case her argument never covered. They didn't stop taking work; they stopped being offered it.

## Who feels it
- **Vesper:** little work, and no way to tell why. We don't know if they've noticed or what they'd say. Nobody has asked them.
- **Handlers like Kip:** whoever looks after Vesper sees someone gone quiet, with no signal in the console and no lever. Handlers across the board report callouts "gone before they could answer" and uneven work (3 of 4 and 2 of 4 interviewed). Tickets understate it, since Kip filed none.
- **Callouts:** with no taken ping rose from 5.5% to 11.1% across the post-4.2 weeks, and is back to baseline now.

## What we'd build instead of changing a number
Seen from Vesper's side and Kip's:

1. **A way back.** A responder whose score is low because of misses, not refusals, gets a fair share of pings until the score reflects how they actually respond. Vesper feels: "I'm getting work again, and I can take it."
2. **A miss isn't a refusal.** Score them separately, as they are already recorded separately. Vesper feels: a short wait doesn't punish them for a buzz they couldn't answer in time.
3. **Kip can see it.** The console flags a responder who has gone quiet, says why in plain words (few pings, mostly missed), and lets the handler act. Kip notices: "I know Vesper has gone quiet before a ticket comes in."

Ideas 1 and 2 are code changes for engineering. Idea 3 needs Sofia. All three are open to your steer.

### Who it's for
Vesper first: a responder in good standing who has been quietly dropped down the list. Kip second: the handler who looks after responders like Vesper and finds out only when a ticket lands, if at all. The same fix helps the other three quiet responders, and the ones who aren't quiet yet.

### What changes once it exists
| | Today | After |
|---|---|---|
| **Vesper** | About 1 ping a week, mostly missed, nothing taken since 17 Aug. Score only moves by taking pings, which they don't get. | Gets a fair share of Old Town pings again. A miss no longer counts as a refusal. Their score can climb back by taking work. |
| **Kip** | Hears "Vesper's gone quiet" from nobody. Sees uneven work and has no explanation or lever. | The console shows Vesper as quiet, says why (few pings, mostly missed), and Kip can act before a ticket is filed. |
| **Callouts** | More pings needed, more with nothing taken. | Back to baseline and staying there, because the quiet responders are in the rotation again. |

### What it deliberately doesn't do
- **It doesn't just change a number.** No quiet tweak to the wait or the weights and calling it handled.
- **It doesn't promise Vesper work.** It gives a fair chance to earn their score back; a responder who keeps missing or turning down will still rank lower.
- **It doesn't let handlers change routing.** Routing stays a release-time config (as today); the console only shows and flags.
- **It doesn't settle the 90s vs 60s question.** That is the separate dev investigation below.
- **It doesn't touch identity.** Everything runs on ping history; nothing links a cover name to a person (Security Policy 4.1).
- **It doesn't redesign ranking or add Availability Confidence or mutual aid.** Those stay on the roadmap.

## Did the 90s to 60s change help? (for a dev to look at properly)
The three ideas above go first. Separately, I'd like a developer to examine the wait change, because our own numbers say it didn't speed anything up:

| | Before 4.2 | After 4.2 |
|---|---|---|
| Gap after a missed ping | 92s | 62s |
| Gap after a turn-down | 22s | 22s |
| Callouts with nothing taken | 5.5% | 11.1% |
| Average pings per callout | 1.23 | 1.39 |
| Average seconds until the taken ping is sent | 7.2 | 17.5 |

Each miss now saves about 30s, but misses per callout rose from about 0.025 to about 0.20, which more than cancels it. People who say no still take about 22s, so the shorter window isn't cutting them off.

What we can't see: `pings` has no answer time, so we don't know whether those who missed would have answered at 70s, or whether the wait or the new weights caused the extra misses. Both changed on 12 Aug.

**Ask for dev:** pull real answer times (or add the field), replay post-4.2 callouts on 90s and on the old weights separately, and report whether the 60s wait got a callout to an accepting responder any faster. Also find out why it was lowered; the wiki and code don't say.

## Not known yet
- Why the wait was cut to 60s. Priya owned it and left no reason; Marcus or Wen may know.
- Whether the 60s wait or the new weights did the damage. The data can't separate them (no response-time or distance fields).
- Whether 4.2 reset scores. They are held in memory in `history.py`, so a release restart may have wiped them. Wen or Marcus can confirm.
- Vesper's own answer times and travel times. Engineering can pull them.
- How Vesper feels. No responder interviews have been done.
- Prior-year baselines, so "August is always soft" is still untested.

## Risks to name
- **Supply:** changes to who gets pinged shift Supply's low-load maintenance scheduling. Night callouts already halved (13.8 to 6.5 a week).
- **Fairness:** boosting quiet responders could send pings to people who really have stopped.
- **Security Policy 4.1:** "Vesper" is a cover name. Nothing here may link it to anyone, and any "why you've gone quiet" message must use ping history only.

## What I'd ask of you
- Agree the direction before engineering touches code: try the three ideas first.
- Have a dev look into the 90s to 60s change in parallel (section above). A revert to 90s is a reversible test if the replay supports it.
- OK for me to ask Wen and Marcus: score reset in 4.2, Vesper's answer and travel times, and a replay of post-4.2 callouts on the old settings.
- Say which of the three ideas you want shaped first.
