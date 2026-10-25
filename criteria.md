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

## 1.

<!-- Your criterion. It must name a number. -->

Overall accuracy is at least 0.65 on the held-out test set, on every one of the three seeds.

**Why this target:**

Three balanced-ish labels means a guesser gets about 33%, and always guessing `reaction` (my biggest label) gets about 41%. 0.65 is clearly above both, and it is about the lowest accuracy I'd call useful for sorting F1 posts. I kept it moderate because I only have 200 posts, 196 of them synthetic, and it has to hold on all three seeds.

---

## 2.

<!-- Your criterion. -->

`reaction` and `hot_take` each have an F1 of at least 0.60 on the held-out set, on every seed.

**Why this target:**

These are my two biggest labels (83 and 71 posts), so they have the most training data and the steadiest scores. They are also the pair my decision rule struggles with, so 0.60 checks that the model learned the boundary and isn't just calling everything `reaction`.

---

## 3.

<!-- Your criterion. -->

`question` has an F1 of at least 0.55 on the held-out set, on every seed.

**Why this target:**

`question` is my smallest label (46 posts, 23%), so only about 7 land in the test split and one wrong prediction moves its F1 a lot. I set it 0.05 looser than the other two for that reason. Questions are the most distinct label (they usually end in `?` or start with how/does/why), so I still expect it to do reasonably well.

---

## 4.

<!-- Your criterion. -->

No label makes up more than 50% of `labels.csv`, and the smallest label is at least 20%.

**Why this target:**

Right now it is 41.5% `reaction`, 35.5% `hot_take` and 23.0% `question`. If I add more posts, especially real ones, this stops me drifting into a dataset where one label dominates and accuracy looks good for the wrong reason. The 20% floor protects `question`, which is already close to it.

---

## 5.

<!-- Your criterion. -->

My agreement with the staff set (30 posts, labelled under the staff taxonomy) is at least 70%, i.e. 21 of 30.

**Why this target:**

This measures labelling consistency, which a model can't fix. If I only agree with someone applying written rules 70% of the time, my own 200 labels are probably noisy and the model's ceiling is low. 70% allows for the posts that are deliberately ambiguous, and a number much lower would tell me my `hot_take` vs `reaction` rule isn't clear.

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
