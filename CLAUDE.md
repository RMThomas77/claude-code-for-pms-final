# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

Sources: Priya's handover (`00-rook/company/notes/handoff-from-priya.docx`, 21 Aug 2026) and the Rook wiki "Company" section (About Rook, Dispatch and Supply one-pagers, Glossary, Team directory, Releases, Q3 roadmap). Not yet read: wiki Research/Customer interviews, Product briefs, routing audit log, `00-rook/code/`, `00-rook/feedback/` (empty). Where Priya's opinion and the wiki differ, they're labelled.

### Me
PM for Rook Dispatch, replacing Priya Raghunathan (sole Dispatch PM for 14 months; left 21 Aug 2026 with no handover overlap). She admits making calls faster than she checked them; weak spots are likely in parts nobody has looked at closely.

### Company and products
Rook sells coordination and provisioning software to independent masked responders and the handlers and quartermasters who support them. Rook doesn't employ responders. ~241 staff; subscription priced per active responder; monthly release train (4.x numbering); three support tiers.
- **Rook Dispatch** (mine, flagship, release 4.2): availability, proximity, who gets pinged for a callout, and whether they take it. Web console for handlers; native phone app for responders (stable since 4.1). Routing configuration ships with a release; handlers can't change it at runtime.
- **Rook Supply**: requisitions, maintenance, failure reports, for handlers and quartermasters. Next: requisition approval chains (4.3).
- **Dependency:** Dispatch writes the Responder Availability Record; Supply reads it and schedules gear maintenance into low-callout-load periods. Any change to how Dispatch calculates availability or load silently changes Supply's scheduling.
- **Hard constraint:** cover identities are never stored or mapped to legal identities. Read Security Policy 4.1 before designing anything touching responder records. Never try to work out who anyone is.

### People
| Name | Role | Notes |
|---|---|---|
| Helen Achebe | Director of Product | My director; owns roadmap and commitments; gives room |
| Marcus Oyelaran | Engineering Manager, Dispatch | Candid; first stop when unsure; can pull numbers |
| Wen Li | Staff Engineer, Dispatch (Berlin) | Built the who-gets-pinged logic; no good doc, so talk to them. Away 14–24 Aug, i.e. just after 4.2 shipped |
| Sofia Marino | Product Designer | Owns console and phone app; ran the September interviews |
| Ravi Menon | Data Analyst (Singapore) | Weekly reporting on how often responders take pings |
| Nadia Hoffmann | Support Lead (Berlin) | Hears handler complaints first; Priya suggests a standing 15 min |

### Vocabulary
- **Responder** takes callouts (not an employee). **Handler** looks after specific responders and is usually who's in the product. **Quartermaster** owns equipment stock and approvals (Supply).
- **Callout**: request to attend an incident. **Ping**: a callout offered to one responder. **Taken** / **turned down** / **missed** (no answer within the ping wait). Turned down and missed both pass the ping on, but are recorded separately.
- **Ping wait** (Priya says "ping timeout"): how long a ping waits before counting as missed; same for everyone.
- **Routing priority**: score ranking available responders. Inputs: proximity (travel-time estimate since 4.1), availability, capability match, recent acceptance history. Turning down or missing pings lowers recent acceptance and so later rank.
- **Capability tags**: flight, structural-entry, hazmat-tolerant, cold-weather, aquatic, crowd-management, de-escalation.
- **Acceptance rate**: pings taken ÷ all pings (taken, turned down, missed). Headline metric, reported weekly in aggregate. **Time-to-accept**: median seconds, ping sent to taken. **Coverage gap**: no available responder had the needed tags (nobody *could* go, which is different from nobody *would*).
- **Mutual aid**: responders covering for each other across areas; unsupported, on the Q4 exploration list.

### Where things stand (as of 6 Oct 2026)
- **Releases:** 4.0 (7 Apr: new console nav, responder profile redesign, routing override audit log); 4.1 (16 Jun: travel-time proximity, bulk callout, push reliability); **4.2 (12 Aug)**: proximity weighted up in routing, **ping wait cut 90s → 60s**, console filters persist, and three defect fixes: (1) duplicate push notification when a ping is sent again, (2) capability tag ordering in the responder detail panel, (3) wrong time zone on the coverage report export. None touch ranking.
- **The problem:** since 4.2, fewer pings are taken and more handlers complain. Support (wiki comments on the 4.2 release page): callout tickets ~3x normal from 15 Aug, still elevated 26 Aug; roughly two thirds "my phone never goes off", one third "buzzed, but it had already gone to someone else" (the latter fits the 60s ping wait; the former is unexplained). No weekly numbers had been pulled by 28 Aug. Console filter persistence is cosmetic; Priya says don't let it eat month one.
- **Unanswered question on the routing change** (Marcus, 14 Aug): does the new weighting apply to responders who keep turning jobs down? The config doesn't distinguish them. Wen Li was away and hasn't answered on the page. Marcus also planned a proper 4.2 regroup once I'd had a week.
- **Priya's view (untested):** mostly seasonal (August is always soft), should recover in September; check that before taking the routing change apart; don't let this become "revert 4.2" because the change was a long-requested fix for responders in wide geographies. September data should now exist, so this can be tested.
- **My reading, to verify:** three things moved at once (seasonality, proximity weighting, ping wait). Because missed pings count against acceptance rate, a shorter ping wait could lower acceptance rate on its own, and each miss also lowers that responder's later rank. The wiki doesn't confirm any of this.
- **Data check (database, through 6–7 Sep):** weekly acceptance was 75–78% before 4.2, fell to 54% the week of 10 Aug, and has recovered to 73% (week of 31 Aug). Missed pings went from 2–6/week to 38, 28, 24, 21; tickets from 5–8/week to 20, 27, 32, 25. Callouts dipped (137–144 → 110–127) and are rising. So partly recovering, not fixed; seasonality alone doesn't explain the missed-ping jump. Only one week of September exists. Not yet done: ticket text themes, per-responder split, time-to-answer.
- **Data access:** the `rook-database` connector is read-only SQL with tables `callouts` (29 Jun–6 Sep), `pings` (outcome: taken / turned down / missed), `responders`, `handlers`, `support_tickets` (29 Jun–7 Sep). Weeks run Monday-start; the 4.2 week (10 Aug) mixes pre- and post-release days.
- **Q3 roadmap** (last reviewed 30 Jun, all owned by Priya, i.e. now orphaned): Who-gets-pinged change, Ping timeout tuning: both shipped in 4.2. **Availability Confidence** (confidence score beside stated availability) was Committed for 4.2 but is not in the 4.2 notes, so it slipped. Requisition approval chains: 4.3, Committed (Supply). Handler phone app and shared cover between responders: Q4, Exploring.
- **Open to-dos:** agree with Helen which squeezed-out 4.2 items (at least Availability Confidence) are still Q3 commitments; write the missing description of how ranking works (with Wen Li); check what Availability Confidence slipping means for the Supply dependency.

### Gaps
Prior-year August/September baselines (needed to test "August is always soft"); data after 7 Sep; what the September interviews found; whether 4.3 is dated.

### Module 2 findings (interviews + support tickets + per-responder pings)
- **Per-responder split:** Vesper, Meteor Mite, The Undertow and Farlight fell from about 12 pings a week to about 1 after 4.2, with 53–64% of their post-release pings missed. Halfmoon and Corporal Ashgrove are down about 20%; the other responders' pings rose. Callouts in those areas held steady, so it isn't lack of demand, and the 73% acceptance recovery is carried by the others. Cause unconfirmed (60s ping wait vs the routing change; no response-time field in the data). Callouts with no taken ping went from 5.5% to 11.1%, and those needing 3+ pings from 1.4% to 5.9%; the latest week is back at baseline.
- **Tickets (147; 107 after 4.2):** 45 are "gone before they could answer" (15) or "few/no pings" (30). All are open and none exist before 4.2. The 30 come from four handlers (Linda Pruitt, Desmond Okafor, Yusuf Demir, Simone Fischer), none of whom were interviewed. The three 4.2 defect fixes look like they worked (no related tickets after release).
- **Interviews (four handlers, 2–5 Sep):** callouts gone before answering (3 of 4), handler alerts (3 of 4), uneven work (2 of 4), small text (2 of 4). Aunt Dot, Kip and Halloran filed no tickets, so the ticket queue misses them; the interviews also sound calmer about quiet responders than the tickets do.
- **Persisted through 4.2 (in both sources):** small status badge text, one alert sound for everything, dark mode, the capability tag legend. Thirteen more recur only in tickets; closed pre-4.2 tickets came back, so "closed" doesn't mean fixed. Filters shipped and are valued, but they reset silently and sometimes keep the wrong user's filters.
- **Supply, not caused by 4.2:** requisition priority does nothing (cracked vest plate waited 11 days), failure reports go unanswered, catalog search is poor. Check that 4.3 approval chains cover urgency.
- **Still open:** ask Wen Li and Marcus how misses weigh into rank, how long "recent" lasts, whether there's any recovery, and for time-to-answer on the six affected responders; find out what "active responder" means for billing; reply to the four quiet-ticket handlers and separately to Dot and Kip.

### Module 3 findings (callout-history.csv + pings, callouts, responders)
- **Weekly acceptance (CSV, 16 responders):** 75–78% for six weeks, 54% (10 Aug), 66%, 67%, 73% (31 Aug). The four quiet responders (Vesper, Farlight, Meteor Mite, The Undertow) had 0–1 pings a week by late Aug and have taken nothing since mid-Aug. Vesper's last taken ping was 17 Aug.
- **Outcome split:** the four's turned-down share stayed about 21%; misses went from 3% to about 49%. The other twelve also rose, from 2% to 13%, so something global hit everyone. The four are the outliers, not the only ones affected.
- **Routing, not demand:** before 4.2 each responder was pinged on every callout in their own area; after, the four stopped being pinged for their own areas (Vesper 13 of 41 Old Town callouts, first ping on 8 of 41, down from 58 of 69), and Nightwell, Captain Vantage and The Longcast took over. Callouts fell about 14% overall (10–17% in the four's areas). Halfmoon and Corporal Ashgrove still get every local callout; their drop tracks falling local demand (Westbury −30%, Northfield −27%).
- **Ruled out:** shared area (the four are in four different areas; Eastgate's other responder, The Gale, gained pings), callout type (free text only, no shift), time of day (misses rose in every part of the day). Night callouts halved (13.8 to 6.5 a week), which may matter for Supply's low-load scheduling.
- **Caveats:** my pre/post split is by week of 10 Aug, which includes 10–11 Aug before the release, so post-release miss rates are probably higher than the 45–53% I counted (the earlier 53–64% figure is likely the cleaner one). The `responders` table has no capability-tag or distance fields, and `pings` has no response-time field. A new session can't see earlier chats unless asked to read them; the Module 2 session was read through the session tools.

### Module 4 findings (dispatch-routing code, read end to end)
- **How ranking works** (`00-rook/code/dispatch-routing/`: config, routing, offer, history, availability): everyone available is ranked by proximity + recent acceptance + capability match, then asked one at a time down the list. Nobody is removed and there is no cutoff score. 4.2 changed the weights from 0.45/0.40/0.15 to 0.60/0.25/0.15 and the wait from 90s to 60s; the code history is a single commit, so only the "was…" comments and the changelog show what changed.
- **Score rules** (`history.py`): everyone starts at 0.5, a take adds 0.08, a turn-down or a miss takes off 0.12 (misses count as refusals), floor 0, ceiling 1. Nothing else raises a score and nothing lets it drift back (open 2019 TODO). The change applied to everyone, not just new responders; I can't tell from the code whether scores reset at release.
- **Working hypothesis (likely, not proven):** the 60s wait caused extra misses that count as refusals, and the heavier weight on distance pushed the four quiet responders down the list. They are now asked only after others say no, so they get few pings and can't earn their score back. Nothing in the data separates the wait from the weights, and there are no response-time or distance fields.
- **Open:** ask Wen Li and Marcus whether scores were reset in 4.2 and for answer times and travel times for Vesper, Farlight, Meteor Mite and The Undertow; ask for a replay of post-4.2 callouts on the old settings. A short reply to Marcus's 14 Aug question is drafted but not sent. Any fix (restore 90s, stop counting misses as refusals, let scores recover) is a code change for engineering to decide.
