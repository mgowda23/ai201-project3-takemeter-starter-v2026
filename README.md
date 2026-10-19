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

TakeMeter sorts comments from r/soccer, Reddit's main football discussion
community, by what kind of contribution they make. Each comment goes into one of
three labels: `analysis` (an argument backed by a checkable fact),
`hot_take` (a confident opinion with nothing backing it), or `reaction`
(in-the-moment emotion about something that just happened). The goal is to tell
a stat-backed argument apart from loud opinion and match-day noise.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Makes a point about anyone or anything in football (players,
teams, managers, referees, clubs, leagues) and backs it with at least one
specific, checkable fact (a stat, score, date, fee, record or named result)
that does work in the argument.

**Example 1:**
> 5-0 vs Palace, 5-0 vs Coventry, 4-0 vs Villa, 3-0 vs Arsenal, second in the league. I have no words for this team. Not seen such an entertaining team in the league in a long time, they're absolutely hammering teams

**Example 2:**
> Under Emery, Villa have taken 59 points out of 33 matches against the Sky Six - that's 1.79 points per match. (Just so we don't have to include our performance under Gerrard.)

### `hot_take`

**Definition:** States a confident judgement about anyone or anything in
football (players, teams, managers, referees, clubs, leagues) with no
checkable support. It may be true, but it isn't argued.

**Example 1:**
> Saka is not the star boy arsenal fans make him out to be and frankly he hasn’t been world class in at least a year.

**Example 2:**
> It’s pretty evident Arsenal overpaid for Eze and it’s a poor decision. It does not seem like he’s interested in playing LW, That money would have been better spent on Rogers this summer instead.

### `reaction`

**Definition:** An immediate response to something that just happened (a
goal, a result, a signing, a decision, a comment in the thread) that expresses
a feeling, a joke or a question rather than a judgement.

**Example 1:**
> what an unbelievable stop!!!

**Example 2:**
> I've seen episodes of Mrs Browns Boys more enjoyable than that good lord

### The hardest boundary

**Which two labels:** `analysis` vs. `hot_take`

**The decision rule I used every time:**
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->

A comment is `analysis` only if it names at least one specific fact someone
could look up (a number, score, date, fee or named match result) **and** that
fact supports the claim. Vague support like "he always disappears in big
games", "everyone knows" or "look at his record" does not count, so those
comments are `hot_take`, however reasonable they sound. Tone does not matter:
an angry comment with a real stat is still `analysis`.

**Second rule (`hot_take` vs. `reaction`):** If the comment passes judgement
on someone's quality or future ("he's finished", "worst signing ever"), it is
`hot_take`, even if it was written mid-match. If it only expresses a feeling
about what just happened ("I can't believe that", "WHAT A GOAL"), it is
`reaction`.

**Jokes and sarcasm:** label by what the comment is actually doing. A
sarcastic "yeah he's definitely worth £100m" is a judgement with no support,
so it is `hot_take`. A pure joke that judges nothing is `reaction`.

**Other cases the three definitions don't settle on their own:**

- **Questions:** a loaded question that carries a judgement ("Why is his first
  touch so bad?") is `hot_take`. A genuine question with no judgement in it is
  `reaction`, because it's a response to the discussion, not an argument.
- **Bare facts:** a checkable fact offered as a contribution ("Most saves in a
  World Cup match? 16 by Tim Howard against Belgium in 2014") is `analysis`,
  even if no opinion is stated.
- **Predictions:** a confident claim about what will happen, with no checkable
  support ("30th Oct has 0-0 written all over it"), is `hot_take`.
- **Off-topic comments** (kits, celebrations, fans): use the same tests. A
  judgement is `hot_take`, a feeling is `reaction`.


---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** Public comments from
[r/soccer](https://old.reddit.com/r/soccer), copied by hand from threads posted
in September and October 2026. I spread them across thread types so no single
match or topic dominates:

- **Post-match threads:** Brighton, Manchester United, Arsenal vs Forest, Real
  Madrid, Liverpool vs City, Spain vs England
- **Live match threads:** Lazio, Lyon, Milan (short, in-the-moment comments)
- **Champions League:** Barcelona vs Galatasaray
- **Stats and [OC] posts:** a Ballon d'Or ranking, a big-six points table,
  Haaland's Norway record (where most `analysis` comments came from)
- **News:** the Manchester City punishment story

I kept whole comments and skipped anything that only made sense next to the
comment it replied to ("this", "called it", a meme reference). I removed two
duplicates, one exact and one reply that quoted another comment, so they
couldn't leak between the training and test splits.

**I have 161 rows, not 200.** Copying comments by hand took much longer than I
expected, so I stopped once I was past the 150 floor rather than rush the
labelling. The cost is that `analysis` is thin (30 rows, so about 5 in the test
split), and my criteria set a looser target for it because of that.

**How I labelled them:** By hand, by me. **No AI pre-labelling.**

1. **Cold set.** 20 rows picked at random, marked `cold` in the note column and
   labelled using only my README definitions. One row I first picked ("This
   Brighton team is insane…") was swapped out of the cold set because the AI
   tool I was using showed a label for it as a formatting example. Its note
   says so. A random hand-labelled row replaced it.
2. **The other 141**, labelled by hand. In practice I labelled these before the
   20 cold rows, not after as the brief suggests, but the cold rows were still
   labelled without any help.
3. **Second-opinion review.** I had an AI tool read my 141 non-cold labels
   against my written rules and list the rows where it would pick differently.
   It flagged 38. I accepted all 38 after reading them, mostly judgements I had
   labelled `reaction` that my own rule calls `hot_take`. Every changed row is
   marked `relabelled after AI second-opinion review` in the note column. The
   20 cold rows were not reviewed or changed.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `hot_take` | 77 | 48% |
| `reaction` | 54 | 34% |
| `analysis` | 30 | 19% |
| **Total** | 161 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "Barca won against Galatasaray, Atleti wins against Man U,
> Villareal wins against Napoli. Yeah La Liga is a farmer’s league alright."
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose `analysis`, because:* it's sarcastic, and my sarcasm rule says to
> label by what the comment is actually doing. The real point is "La Liga is not
> a weak league", and it's backed by three specific, checkable results. The
> facts do the work in the argument, so the tone doesn't make it a `hot_take`.

**2.**
> *The post:* "Vuskovic's mentality will make him be one of the best CB in the
> world. He lost a single duel today you could see in his reaction how much
> that pissed him off."
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose `analysis`, because:* "lost a single duel today" is a match stat
> someone could look up, and it's offered as evidence for the claim. It's the
> weakest `analysis` in my set, though. The claim ("one of the best CB in the
> world") is far bigger than one stat can support, and someone else could fairly
> call it a `hot_take`.

**3.**
> *The post:* "If City is not punished severely enough I legimately think the
> Premier League collapses. Theyve spun off the first division before and tried
> to other times. I don't think theres any circumstance where
> Liverpool/United/Arsenal are will to play in the same league City is in over
> the next decade."
>
> *Could have been:* `reaction` or `hot_take`
>
> *I chose `hot_take` (after first labelling it `reaction`), because:* it reads
> like an angry response to the news, which is why I first put it in `reaction`.
> But it confidently predicts the league collapsing, with no checkable support
> ("spun off the first division before" isn't a specific fact). My rule says a
> judgement or prediction is `hot_take` even when it's emotional. This was the
> pattern behind most of the 38 relabels: I had been reading angry opinions,
> especially about the City story, as reactions.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:**

**Settings:** <!-- epochs, learning rate, batch size, seed -->

**Anything I changed from the defaults, and why:**

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Moment 2**

- *What I asked for:*
- *What came back:*
- *What I changed:*

**Pre-labelling disclosure:**

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
