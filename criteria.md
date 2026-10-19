# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy is at least 0.64 on the held-out test set, on all three seeds.

**Why this target:** `hot_take` is 77 of my 161 posts (48%), so a model that
always guessed `hot_take` would already score about 0.48. Random guessing across
three labels gets about 0.33. With roughly 25 posts in the test split, 0.64
means at least 16 of 25 right, which is four posts clear of the always-`hot_take`
guesser. I'd only call it useful if it beats that guesser by a real margin.
I didn't go higher because it only has about 112 training posts and the target
has to hold on all three seeds.

---

## 2. No more than 60% of the model's test-set predictions are `hot_take`, on all three seeds.

**Why this target:** `hot_take` is my biggest label, so the cheapest way for the
model to look decent is to lean on it. About 48% of my test posts really are
`hot_take`, so predicting it for up to 60% leaves some room for honest
mistakes. Anything above that means it's guessing the majority label instead
of telling the labels apart. I'll check it from the column totals of the
confusion matrix in `results.json`.

---

## 3. `hot_take` and `reaction` each have an F1 of at least 0.55, and `analysis` has an F1 of at least 0.40, on all three seeds.

**Why this target:** `analysis` is my smallest label, with only 30 posts (19%), so
only about 5 land in the test split, and each one moves its F1 a lot between
seeds. That's why its bar is lower. `hot_take` (about 12 test posts) and
`reaction` (about 8) have enough examples to hold a higher bar. Even 0.40 for
`analysis` means it has to find some of those posts rather than ignore the label.

---

## 4. When I label the 30 staff posts under the staff taxonomy, I agree with the staff labels on at least 22 of 30 (73%).

**Why this target:** When I had an AI second-opinion check of my own 141
non-cold labels against my written rules, we disagreed on 38 of them, which is
about 73% agreement. Most of those were judgements I had labelled `reaction` that
my own rule calls `hot_take`. Applying someone else's written rules should be
at least as reliable as that. If I can't reach 22/30, that's evidence that my
labelling, not the model, is a source of error.

---

## 5. On the test set, the model's most-confident third of predictions is at least 0.20 more accurate than its least-confident third, on all three seeds.

**Why this target:** Each third is only about 8 posts, so one post is about 12.5
points. A gap of 0.20 means the confident guesses get at least two more
right than the unsure ones, which is more than one lucky post. If the gap is
smaller, the model's confidence tells you nothing, and you couldn't use it to
decide which predictions to trust. The notebook reports both numbers as
`accuracy_most_confident_third` and `accuracy_least_confident_third`.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
