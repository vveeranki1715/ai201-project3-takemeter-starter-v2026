# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->
My community is Formula 1 fans: the posts and comments people write about races, drivers and teams on forums like r/formula1. The classifier reads one post and sorts it into what kind of thing the person is doing: `reaction` (responding to something that just happened), `hot_take` (a confident claim with nothing to back it up) or `question` (asking for information). The point is to tell the heat of the moment apart from opinion and from curiosity, which is most of what an F1 discussion thread is made of.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `reaction`

**Definition:** An immediate response to something that just happened, expressing the poster's feeling about it.

**Example 1:**
> Congratulations to Lance Stroll for Completing his 1st Full Race This Year

**Example 2:**
> Max via Viaplay: "My radio totally didn't work. They couldn't hear me at all, but luckily i was able to hear them. sometimes you wanna ask something, you want to give certain feedback... but luckily 90% of the things GP said was what i wanted to ask, so, it was totally ok."

### `question`

**Definition:** A post that asks other people for information or an explanation.

**Example 1:**
> How do the drivers stay hydrated during the race?

**Example 2:**
> Does anyone have a link to the onboard from that overtake?

### `hot_take`

**Definition:** A confident claim or opinion about a driver, team or the sport that offers no support for itself. It might be true, but it isn't argued.

**Example 1:**
> DRS makes racing artificial, scrap it

**Example 2:**
> Every new circuit is the same design with different colours

### The hardest boundary

**Which two labels:**
`hot_take` and `reaction`

**The decision rule I used every time:**
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->

If the post is responding to a specific moment that just happened (a pass, a pit call, a crash, a result), it's `reaction`, even if it is phrased as a claim ("that pit call lost them the race"). If it's a general claim that would still make sense next week and doesn't depend on one moment ("street circuits make for boring racing"), it's `hot_take`.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**
Only 4 of the 200 posts in `labels.csv` are real: I copied them from F1 fan discussion (TODO: say which thread or site). The other 196 are synthetic. I could not collect more real posts in the time I had, so Claude wrote them in the style of F1 fan comments. They are marked `synthetic` in the `note` column of every row. They are not posts from the community, so the results of this project say little about how real F1 fans write. Replacing them with real posts is the first thing I would do next.

**How I labelled them:**
<!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->
All 200 rows were labelled by Claude, using the three definitions above and the decision rule for `hot_take` vs `reaction`. 170 were labelled as it wrote them, and I asked it to label the other 30 afterwards (my 4 real posts plus 26 synthetic ones). I have not yet read and corrected every label, so none of them count as labelled cold by me. TODO: read through at least the 30 most recently labelled rows and the 4 real posts, change any I disagree with, and say here how many I changed.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `reaction` | 83 | 41.5% |
| `hot_take` | 71 | 35.5% |
| `question` | 46 | 23.0% |
| **Total** | 200 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* Lando being told off for drinking coke in the cooldown room: "I just did a race. So I'm allowed to drink what I would like. thank you so much."
>
> *Could have been:* `reaction` or `hot_take`
>
> *I chose `reaction`, because:* it is about a specific moment that had just happened (he was told off straight after a race), so my decision rule sends it to `reaction`. It is a quote from a driver and not the poster's own feeling, and it makes a point without supporting it, which is what pulled it towards `hot_take`.

**2.**
> *The post:* Max Verstappen is apparently named after the MotoGP driver, Max Biaggi
>
> *Could have been:* `hot_take` or `reaction`
>
> *I chose `hot_take`, because:* it is a plain piece of trivia, so none of my three labels fits it well. It isn't a response to anything that just happened, which rules out `reaction`, and it is a claim about a driver that offers no support, which is the nearest to my `hot_take` definition. It isn't an opinion, so this is the weakest fit in my data.

**3.**
> *The post:* Congratulations to Lance Stroll for Completing his 1st Full Race This Year
>
> *Could have been:* `reaction` or `hot_take`
>
> *I chose `reaction`, because:* it responds to a specific race that had just happened. The wording is sarcastic, so it also reads as a claim about Stroll's season in general, which is what made me pause, but the trigger is that one race, so `reaction`.

---

## The Training Run

**Base model:** `distilbert-base-uncased`, fine-tuned on my labels.

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, 128 tokens per post, seed 42. Trained on an Apple Silicon GPU (`mps`).

**Anything I changed from the defaults, and why:**
Nothing. All the settings are the notebook defaults. The only thing I changed in the notebook was the `LABELS` list, from the starter's `["analysis", "hot_take", "reaction"]` to `["question", "hot_take", "reaction"]` to match my CSV. The notebook's data check flagged the mismatch before I trained.

**Split sizes:** 139 train / 31 validation / 30 test (70/15/15, stratified).

| Label | Train | Val | Test |
|---|---|---|---|
| `question` | 32 | 7 | 7 |
| `hot_take` | 49 | 11 | 11 |
| `reaction` | 58 | 13 | 12 |

`question` has only 7 examples in the test split, under the roughly 8 the notebook suggests, so its scores will move a lot between seeds. Also, 196 of the 200 posts are synthetic, so the data is cleaner and more patterned than real fan comments would be.

---

## How I Used AI

I used Claude (Claude Code) for a large part of this project, so I am listing exactly what it did and what I did.

**What Claude did:**
- Wrote 196 of the 200 posts in `labels.csv` in the style of F1 fan comments (the other 4 are real posts I copied).
- Assigned the label on all 200 rows, using my definitions.
- Drafted the five criteria in `criteria.md` and the first version of several README sections, which I then reviewed.
- Found why the notebook's data check was failing (the leftover `analysis` label) and told me how to fix it.

**What I did:**
- Chose the community (F1 fans), the three labels and the `hot_take` vs `reaction` decision rule.
- Collected the 4 real posts and decided to continue with synthetic data for the rest, because I could not collect more in the time I had. I accepted the cost: the project says little about how real F1 fans write.
- Decided to skip labelling a cold set myself, so none of my labels are cold.
- Made the `LABELS` fix in the notebook, ran sections 1 to 5, and read the output.

**Moment 1**

- *What I asked for:* 200 labelled F1 posts, using my three label definitions and my decision rule, because I did not have enough real posts.
- *What came back:* 196 synthetic posts with labels, 83 `reaction`, 71 `hot_take` and 46 `question`, all marked `synthetic` in the `note` column.
- *What I changed:* I did not correct any labels. I kept the data and disclosed it in this README, in the dataset section and here. Reading and correcting the labels is still undone.

**Moment 2**

- *What I asked for:* help getting the notebook to train, after the kernel would not start and then the data check printed `[FIX ME]`.
- *What came back:* the kernel failure was a slow first `import torch` that timed out, and the `[FIX ME]` was the starter's `LABELS` list still containing `analysis` instead of `question`.
- *What I changed:* I restarted the kernel, changed `LABELS` to `["question", "hot_take", "reaction"]`, re-ran sections 2 to 5, and got `results.json` and `test_split.csv`.

**Pre-labelling disclosure:**
All 200 labels were assigned by Claude, and I did not read and correct them. So none of them are cold labels, and the dataset has not been through the review step the assignment describes. I am saying this plainly because it affects how far to trust the results.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
