# Acceptance criteria — FitFindr

Five criteria that say what "working" means for this agent, written in unit 3
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"The agent handles errors"* is an opinion.
*"When search returns nothing, the agent stops before calling the second tool,
in 5 of 5 tries"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter one. A reason that says something about your tools, your loop, or the
data earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

**Two are written for you. You write three.**

---

## 1. A matching query completes all three tools

Given a query that matches at least one listing, the agent completes all three
tool calls and returns a fit card — in at least 4 of 5 tries.

**Why this target:**
<!-- Why 4 of 5 and not 5 of 5? Something about your search, probably —
     "my search is a plain keyword match and some phrasings will miss" is a
     real answer. -->
search_listings() scores by keyword overlap against description which means a phrasing that doesn't share literal tokens with any listing's title/description/style_tags can score zero even when a human would call it a match. Because of this search-coverage gap, demanding 5/5 would be penalizing the tool for something outside the loop's control. 4/5 accepts that one phrasing in five might miss on vocabulary alone, while still catching real regressions.
---

## 2. An impossible query stops before the second tool

Given a query that matches no listings, the agent stops before calling
`suggest_outfit` and returns a message naming what to change — 5 of 5 tries.

**Why this target:**
<!-- Why is 5 of 5 reasonable here when criterion 1 isn't? What's different
     about this path? -->
There's no scoring ambiguity in this criterion: an empty-result query means search_listings returned [] and not None or an exception. The loop either checks if not results: stop or it doesn't. Since this path has no model-call or scoring variance in it, any failure is a real loop bug, so a target of 5/5 is reasonable.
---

## 3. Something about state

<!-- YOU WRITE THIS ONE.

     How would you know that the item your search found is the same item the
     next tool received? Name something countable or observable.

     This is the criterion people find hardest, because state failure doesn't
     look like state failure — it looks like a tool problem. Something that
     compares session["selected_item"] against what actually reached
     suggest_outfit is the shape you're after. -->

Given a query that matches a listing, the listing_id stored in session["selected_item"] after search is the same as the listing_id received by suggest_outfit — 5 of 5 tries.

**Why this target:**

If the matched listing output from search_listings() and the input to suggest_outfit do not have the same unique listing ID, that's a state problem. It should be a binary check for a simple handoff so 5 of 5 tries is fair.

---

## 4. Something about the fit card

<!-- YOU WRITE THIS ONE.

     The fit card calls a model, so the same input can produce different words
     each time. That's not a bug — it's the nature of the tool. So what would
     make it acceptable?

     Think about what you'd actually be unhappy to see. A caption that never
     mentions the price? Two different items producing the same opening
     sentence? A card longer than a caption anyone would post? Any of those can
     be turned into a number. -->

Every fit card mentions the item's price and platform it's paired with — 5 of 5 times.

**Why this target:**

This is the one thing I'd actually be unhappy to see missing. It's a basic necessity in the output string of create_fit_card regardless of model variance and should always pass so 5 out of 5 is fair.

---

## 5. Your choice

<!-- YOU WRITE THIS ONE TOO.

     Pick something you actually care about getting right. Speed, the empty
     wardrobe path, what happens when the model can't be reached, whether the
     search respects a price ceiling — anything, as long as it names a number
     or an observable outcome. -->

Given a session that has no wardrobe items, the agent calls
`suggest_outfit` and returns a non-empty message, not an exception, not "" — 5 of 5 tries.

**Why this target:**

Simliar to criterion 2, there's no scoring ambiguity in this criterion: an non-empty message means suggest_outfit returned something and not an empty message or an exception. Since this is a deterministic branch with empty wardrobe as a checkable precondition, a target of 5/5 is reasonable and it gives me something to deliberately break and verify. 
---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 4 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 4. Something about the fit card

         The fit card is different every time.

         **Why this target:** ...

         > **Revised in unit 4:** For 5 different items, the 5 fit cards share
         > no opening sentence.
         >
         > **Why revised:** "different" wasn't checkable — two cards that
         > differed by one word still counted. The new version is something I
         > can actually score.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said the empty search stops it 5 of 5 times, but I got 3 of 5,
            so 3 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
