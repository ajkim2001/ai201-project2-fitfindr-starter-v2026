# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> ```
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Three or four sentences: what a user asks for, and what they get back. -->
FitFindr is an app where the user describes a piece of clothing they want to thrift. The app will find a listing that matches that item, suggest an outfit to pair it with, and then write a short social media caption about it in the form of a "fit card". If there is no match, the app will recommend some changes to the query. 


---

## Tool Inventory

<!-- Four lines per tool. This is worth 2 points and it's the single most
     common place students lose them.

     "Returns a list" earns NOTHING. The description has to say what is IN
     the list.

     The empty case isn't optional either — it's the thing your loop branches
     on, and if you don't decide it here you'll discover it as a crash in
     Milestone 5. -->

### `search_listings`

- **What it does:** Search the listings data for items matching a description, and optionally a size and a price ceiling.
- **Inputs:** 'description' (str), 'size' (str), 'max_price' (float) 
- **Returns:** A list of matching listing dicts, best match first. Each with id, title, description, category, style_tags (list), size, condition, price (float), colors (list), brand (str or None) platform. 
- **When it has nothing:** It returns an empty list, not None or an exception, when nothing matches.

### `suggest_outfit`

- **What it does:** Given a thrifted item and the user's wardrobe, suggest one or two outfits.
- **Inputs:** 'new_item' (dict), 'wardrobe' (dict)
- **Returns:** A non-empty string with outfit suggestions. 
- **When it has nothing:** With an empty wardrobe, return general styling advice.

### `create_fit_card`

- **What it does:**  Write a short caption someone would actually post about the find.
- **Inputs:** 'outfit' (str), 'new_item' (dict)
- **Returns:** A two-to-four sentence caption that reads like a real post rather than a product description. It should mention the item and its price and platform once each, and be specific about the vibe. 
- **When it has nothing:** If `outfit` is empty or whitespace, it returns a descriptive message rather than raising.

---

## Planning Loop

<!-- Your branch rule, stated as a rule — the condition AND both paths — plus
     the file and function that holds it.

     Like this:
       "If search_listings returns an empty list, put a message in the session
        and stop. Otherwise take the first result and go to suggest_outfit."
        — agent.py::run_agent

     The grader checks your code against what you claim here, so the file and
     function have to be real. -->

**Branch rule:** If search_listings returns an empty list, put a message in the session and stop. Otherwise take the first result and go to suggest_outfit.

**Where it lives:** `agent.py::run_agent`

**How the query is parsed:** <!-- regex, string splitting, or asking the model — say which --> Query will be parsed by asking the model.

**What moves through the session:** <!-- which fields, in what order --> A parsed description, size, and max_price is put into session["parsed"]. search_listings() is called with what was parsed and the results are put in session["search_results"]. If nothing came back, puts a message in session["error"] saying what the user could change. An item is put in session["selected_item"] and is used to call suggest_outfit().

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask 'vintage graphic tee under $30, size M'
  Found:    Y2K Baby Tee — Butterfly Print — $18.0 on depop

  Outfit:   Pair the butterfly baby tee with your baggy dark-wash straight-leg jeans and chunky white sneakers for an effortless Y2K streetwear look; throw on the slightly cropped vintage black denim jacket over top and sling the black crossbody bag across for an easy day out. Alternatively, lean into a softer, retro-contrast vibe by tucking the tee into your wide-leg khaki trousers, accented with the brown leather belt and finished with those same chunky white sneakers.

  Fit card: Channeling peak Y2K mall culture with this adorable butterfly baby tee. It's giving effortless early 2000s streetwear when paired with baggy dark wash denim. Grab it on depop for $18.00 before I change my mind and keep it.

1 model calls this session, 2 served from cache, 96 prompt + 23 output tokens
```

**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"
[{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressed graphic. Crew neck. Fits boxy. Well-loved but no holes or major damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_011', 'title': 'Low-Rise Cargo Pants — Khaki', 'description': 'Y2K era low-rise cargo pants. Lots of pockets. Khaki color, slightly distressed at the hems. Great for layering with a long tee.', 'category': 'bottoms', 'style_tags': ['y2k', 'cargo', '2000s', 'streetwear'], 'size': 'W29', 'condition': 'fair', 'price': 27.0, 'colors': ['khaki', 'tan'], 'brand': None, 'platform': 'poshmark'}, {'id': 'lst_015', 'title': 'Vintage Graphic Hoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to the worn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}]
```

```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"
For an effortless, off-duty look, pair the medium-wash Levi's 501s with your fitted white ribbed tank top, layered under the slightly cropped vintage black denim jacket, and finish with chunky white sneakers and the black crossbody bag for a classic, casual streetwear vibe. Alternatively, lean into a cozy, nineties-inspired aesthetic by tucking the oversized grey crewneck sweatshirt into the jeans, cinched with the brown leather belt, and ground the outfit with your black combat boots.
```

```
$ AI201_CACHE=0 python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"
Nothing beats a genuinely broken-in pair of 501s with that exact fade at the knees we all try to fake. Toss them on with crisp white sneakers for the ultimate off-duty look. Grab this medium wash staple for $38.00 over on my depop before I change my mind.
```

---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- *What I asked for:* I asked Claude to attack my criterion to make sure they were well written for effective checking.
- *What came back:* It suggested that I be more specific, to cover other potential cases.
- *What I changed:* I changed it to include what to return specifically 'returns a non-empty message, not an exception, not ""'.

**Moment 2**

- *What I asked for:* I want to discuss the "why" reasoning for criterion 1, because I believed that my query would be parsed by asking the model.
- *What came back:* The AI reasoning explained that search_listings does not call the model, it searches scores by keyword overlap against description. It explained a fair argument for the 4/5 target based on keyword-overlap vocabulary gaps.
- *What I changed:* I understood this better that the search_listings tool is using keyword overlap and the model will actually be used in the query parsing mentioned in the branch rule. I was better able to write a why for criterion 1.

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Run Log — Before

<!-- Five criteria, five tries each, in this exact format.

     Five, because your criteria are written out of five. Mark each try PASS
     or FAIL, count the passes, and read that count against your target — a
     row targeting 4 of 5 with three PASS cells is MISSED (3/5).

     `python run_eval.py --label before` runs everything and writes the table
     into results/. Paste it here and fill in the verdicts. -->

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. Matching query completes all three tools | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. Impossible query stops before the second tool | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. selected_item reaches suggest_outfit unchanged | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 4. Fit card names price and platform | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 5. Empty wardrobe gets non-empty advice | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |

Full output for all five scenarios, five tries each: `results/run_2026-10-07_1624_before.md`.

**Real output, one try per criterion**, pasted as text, naming the file and
function that produced it:

**Criterion 1** — `agent.py::run_agent` (try 1, "matching query completes", query `vintage graphic tee under $30`):

```
- selected_item: Y2K Baby Tee — Butterfly Print ($18.0, depop)
- search_results: 10

Fit card:
Living out my early 2000s pop star fantasy in this butterfly print baby tee. Just dropped this little pink and purple dream on depop for $18.00 before I change my mind and keep it. Trust me, you need this crop length for your next baggy denim fit.
```

**Criterion 2** — `agent.py::run_agent`, the branch on `tools.py::search_listings`'s empty-list return (try 1, "impossible query stops early", query `designer ballgown size XXS under $5`):

```
- stopped early: yes — No listings matched 'designer ballgown' (size XXS, under $5). Try broader keywords, a different size, or a higher price ceiling.
- selected_item: (none)
- search_results: 0
```

**Criterion 3** — `trace.py::step`, captured by `run_eval.py::run_once` (try 1, "state: selected_item reaches suggest_outfit", query `khaki cargo pants under $30`). The `selected_item` line and the `suggest_outfit` trace step show the same title, price, and platform:

```
- selected_item: Low-Rise Cargo Pants — Khaki ($27.0, poshmark)

[3] suggest_outfit
      in:  Low-Rise Cargo Pants — Khaki ($27.0, poshmark)
      →    criterion 3: compare this item against selected_item above
```

**Criterion 4** — `tools.py::create_fit_card` (try 1, "fit card names price and platform", query `leather belt under $15`):

```
Obsessed with this leather belt I just scored on thredUp for only $12.00! It has the best vintage Western energy and is going to look so good half-tucked into some baggy jeans. Definitely my new favorite accessory for tying an effortless earth-tone fit together.
```

**Criterion 5** — `tools.py::suggest_outfit`, the empty-wardrobe branch (try 1, "empty wardrobe", query `denim jacket under $50`):

```
Lean into the jacket's structured 80s proportions by pairing it with high-waisted, wide-leg olive green utility pants and a snug, ribbed black tank top, finished off with chunky black loafers for an effortless, downtown-cool vibe. Alternatively, create a playful contrast with feminine textures by layering the cropped denim over a black floral slip dress paired with beat-up white canvas sneakers and a canvas tote bag for an easy, coffee-run-ready look.
```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Matching query completes all three tools | 4 of 5 | MET | 5/5 tries had a non-None `fit_card`, `outfit_suggestion`, and `selected_item` — counted the passes in `results/run_2026-10-07_1624_before.md` and read 5 against the 4-of-5 target. |
| 2 | Impossible query stops before the second tool | 5 of 5 | MET | 5/5 tries had `session["error"]` set and `fit_card` still `None`, with the trace stopping at step 3 (`branch`) before any `suggest_outfit` step appeared. |
| 3 | selected_item reaches suggest_outfit unchanged | 5 of 5 | MET | 5/5 tries had the `selected_item` line and the `suggest_outfit` trace step's `in:` line showing the identical title/price/platform. |
| 4 | Fit card names price and platform | 5 of 5 | MET | 5/5 fit cards for the leather belt scenario mentioned both `$12.00` and `thredUp` — read each of the five pasted cards by eye. |
| 5 | Empty wardrobe gets non-empty advice | 5 of 5 | MET | 5/5 tries on the empty-wardrobe scenario returned a non-empty `outfit_suggestion` string, no crash, no empty string. |

**Diagnoses**

Nothing missed this run — all five criteria held at their stated target, not just on average. 

One thing to note is that the real output underneath wasn't clean: one of `suggest_outfit`'s outputs (criterion 3, try 1) leaked markdown bold — `"...your fitted **white ribbed tank top**..."`. No criterion checks for markdown, so it didn't cost a PASS, but it's a real defect in the tool's output.

Honestly, three of the five (2, 3, 5) are deterministic branch checks with no model call in the path being tested — once the branch logic is correct at all, it passes every time by construction, so running it five times doesn't add information beyond running it once. Of those, **criterion 3 is the one I'd tighten**. Criterion 2's risk is real (a genuine `if` bug would show up), and criterion 5 at least depends on `suggest_outfit`'s empty-wardrobe branch doing something. But criterion 3's scenario ran the *same* item through the *same* query five times — it can't catch an item-specific bug (e.g., a listing missing a field that breaks the handoff) because it never varies the item. A tighter version would run the state check across 5 different matching queries/items instead of one query five times, so the five tries are actually five different pieces of evidence rather than five repeats of the same one.

---

## Loop Trace

<!-- One full run, printed step by step, with the MCP call visible in it.

     `python app.py ask '...' --trace` once you've added the trace.step()
     calls in Milestone 2.

     Worth pasting BOTH the happy path and the empty-search path. The empty
     one should be visibly shorter, because it stops. If your two traces are
     the same length, your branch isn't working — and this is the fastest way
     anyone will ever find that out. -->

**Happy path**

```
$ python app.py ask 'vintage graphic tee under $30, size M' --trace
[1] _parse_query
      in:  vintage graphic tee under $30, size M
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: 8 items: Y2K Baby Tee — Butterfly Print, Mesh Long-Sleeve Top — Black, 90s Silk Slip Dress — Floral, Midi Length … +5 more
[3] suggest_outfit
      in:  dict with keys: new_item, wardrobe
      out: Pair the butterfly baby tee with your baggy dark-wash straight-leg jeans and chunky white sneakers for a class…
[4] create_fit_card
      in:  dict with keys: outfit, new_item
      out: Channeling peak 2000s mall vibes with this butterfly print baby tee. Just listed this cropped cutie on depop f…

  Found:    Y2K Baby Tee — Butterfly Print — $18.0 on depop

  Outfit:   Pair the butterfly baby tee with your baggy dark-wash straight-leg jeans and chunky white sneakers for a classic Y2K streetwear look, then throw on the vintage black denim jacket for an easy, slightly edgy contrast. For an alternate vibe that leans into early 2000s mall-goth, wear the baby tee with the same dark-wash jeans and black combat boots, layering the black cropped zip hoodie unzipped over top to let the pink and purple butterfly graphic peek through.

  Fit card: Channeling peak 2000s mall vibes with this butterfly print baby tee. Just listed this cropped cutie on depop for $18.00 before it's gone. Pair it with baggy dark-wash denim and chunky sneakers for the ultimate nostalgic fit.

3 model calls this session, 711 prompt + 180 output tokens
```

**Empty search**

```
$ python app.py ask 'industrial scuba diving helmet, size 4XL, under $2' --trace
[1] _parse_query
      in:  industrial scuba diving helmet, size 4XL, under $2
      out: dict with keys: description, size, max_price
[2] search_listings (via MCP)
      in:  dict with keys: description, size, max_price
      out: [] (empty)
[3] branch
      →    search_results empty — stopping before suggest_outfit

  No listings matched 'industrial scuba diving helmet' (size 4XL, under $2). Try broader keywords, a different size, or a higher price ceiling.

0 model calls this session, 1 served from cache
```

**On the MCP move:** 
`search_listings` is now registered as an MCP tool in `mcp_server.py`. 
In `agent.py::run_agent`, the direct `search_listings` call became `mcp_client.call_tool("search_listings", {...})` — with the same arguments, same return shape, just routed through the MCP client instead of a plain Python import. 
The rewire worked without changing behavior: re-running the same full query
("vintage graphic tee under $30, size M") returned the identical item, outfit, and fit card as before the move, and the empty-search and empty-wardrobe paths still behave the same way.

---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:** Added to `suggest_outfit`'s system prompt in `tools.py`: "Plain prose only — no markdown, no asterisks, no bullet points."

**Which failure it was meant to fix:** In the before-run, one of `suggest_outfit`'s outputs leaked markdown bold formatting — `"...your fitted **white ribbed tank top**..."` (criterion 3, try 1). None of my five criteria technically failed because of it, but it's a real defect: that string gets displayed and fed into `create_fit_card`'s prompt as plain text, so literal asterisks would show up as garbage in a UI that doesn't render markdown.

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. Matching query completes all three tools | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. Impossible query stops before the second tool | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. selected_item reaches suggest_outfit unchanged | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 4. Fit card names price and platform | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 5. Empty wardrobe gets non-empty advice | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |

Full output: `results/run_2026-10-07_1644_after.md`.

**Did it help, and how do I know:** Partially. It did exactly what it was
scoped to do — across all 20 `suggest_outfit` calls in the after-run, zero outputs contained markdown, versus 1 in the before-run. The targeted tool is fixed.

But it didn't fix the underlying category of problem: in the same after-run, try 2 of the "fit card names price and platform" scenario produced `"...the classic Western buckle adds the *exact* right amount of detail."` — markdown italics, this time from `create_fit_card`, which I never touched. None of my five criteria check for markdown, so this didn't move any PASS/FAIL cell — the run log looks identical to the before-run, all five still MET (5/5). 

The honest read is that I fixed one tool's instance of a problem that two of my three model-calling tools share, because I diagnosed it narrowly (one leak in one tool's output) instead of broadly (neither model-calling tool's prompt rules out markdown). A more complete fix would add the same line to `create_fit_card`'s system prompt too.

---

## What's Still Broken

None of my five criteria are missed — both the before-run and the after-run
hit 5/5 on all five. But two real things are still broken that my criteria
don't catch.

**`create_fit_card` can still leak markdown.** The improvement only added the
"plain prose, no markdown" instruction to `suggest_outfit`'s system prompt.
`create_fit_card`'s system prompt (`tools.py::create_fit_card`) has no such
line, and the after-run caught it doing this —
`"...the classic Western buckle adds the *exact* right amount of detail."`
The fix is the same one-line prompt addition, applied to the other tool. I
stopped here because I'd scoped this unit's improvement to one change, and I
wanted the before/after comparison to isolate that one change rather than
fix both tools and not know which one mattered. This is the next thing I'd
do.

**Criterion 3's scenario can't catch an item-specific bug.** I flagged this
in Verdicts and Diagnoses: the "state" scenario runs the *same* query five
times, so all five tries exercise the identical code path on the identical
item. If the state handoff broke only for items missing a field — say, the
many listings where `brand` is `None` — this scenario would never find out,
because it never varies the item. I'd rewrite the scenario to cycle through
5 different matching queries instead of repeating one, but doing that means
either adding per-try query lists to `scenarios.py`'s shape or changing how
`run_eval.py` iterates tries, and I didn't want to touch `run_eval.py` itself
without checking that in first.

---

## Stretch: A Second Measured Improvement

**Declared change (written before building it):** "What's Still Broken" named
that `create_fit_card`'s system prompt never got the "plain prose, no
markdown" instruction that `suggest_outfit`'s did in the first improvement —
and the after-run caught `create_fit_card` leaking markdown italics
(`"...adds the *exact* right amount of detail."`) as a direct result. The
second improvement adds the same one-line instruction to
`create_fit_card`'s system prompt in `tools.py`, and nothing else. I'll run
`python run_eval.py --label after2` against the same five scenarios and log
a third run in the same table format, then report honestly whether it
helped.

**What I changed:** Added "Plain prose only — no markdown, no asterisks, no
bullet points." to `create_fit_card`'s system prompt in `tools.py`.

**Which failure it was meant to fix:** The markdown leak in `create_fit_card`
observed in the after-run (try 2 of the "fit card names price and platform"
scenario) — the same category of defect the first improvement fixed in
`suggest_outfit`, now applied to the one tool left out of that fix.

### Run Log — After (second improvement)

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1. Matching query completes all three tools | 4 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 2. Impossible query stops before the second tool | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 3. selected_item reaches suggest_outfit unchanged | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 4. Fit card names price and platform | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |
| 5. Empty wardrobe gets non-empty advice | 5 of 5 | PASS | PASS | PASS | PASS | PASS | MET (5/5) |

Full output: `results/run_2026-10-07_1709_after2.md`.

**Did it help, and how do I know:** Yes, and this time cleanly — I checked
the entire after2 run for markdown and found zero instances across
all 25 `suggest_outfit` and `create_fit_card` calls combined, versus 1 in the
original before-run and 1 more in the first after-run. The fix generalized to
the tool it was scoped to.

One side effect worth noting, not a regression: in the criterion-4 scenario
(leather belt, $12.00), 3 of the 5 fit cards this run wrote the price in
words — "twelve dollars," "twelve bucks," "12 dollars" — instead of "$12.00."
That still satisfies the criterion as written (it mentions the price; the
criterion never required digit formatting), so all five tries still PASS. But
it's a real behavior change I didn't predict: telling the model to avoid
"asterisks" and "bullet points" apparently nudged some outputs away from
`$12.00`-style notation too, maybe reading it as adjacent to the discouraged
formatting. If a future criterion ever required a specific price format
(e.g., "the fit card includes the `$` symbol"), this change would newly put
that at risk — exactly the kind of thing that'd only show up by actually
running it and reading the output, not by reasoning about the prompt in the
abstract.

<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
