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

## 1. Overall accuracy on the held-out test set is at least 0.65 across all three seeds.

<!-- Your criterion. It must name a number. -->

**Why this target:**
With three labels (`tactical_analysis`, `hot_take`, and `banter`), random guessing gets 0.33 and guessing our biggest class (`hot_take` at 39%) gets 0.39. Setting the bar at 0.65 means the model has to beat the majority baseline by 26 percentage points while still being realistic enough to hold across all three random seeds on a 200-post dataset.

---

## 2. The `tactical_analysis` label achieves an F1 score of at least 0.65 on the held-out set across all three seeds.

<!-- Your criterion. -->

**Why this target:**
The main point of TakeMeter on `r/soccer` is finding real tactical commentary buried in post-match noise. Because our decision rule ties `tactical_analysis` to specific checkable words (stats like xG, formations, and pressing), the model has clear clues to learn from, so it should hit at least 0.65 F1.

---

## 3. Our smallest label (`banter`) achieves an F1 score of at least 0.55 on the held-out set across all three seeds.

<!-- Your criterion. -->

**Why this target:**
`banter` is our smallest category at around 58 examples, which leaves only about 9 to 11 examples in the test split. Because a couple of sarcastic soccer jokes ("masterclass", "football heritage") can easily look like serious posts and swing the score on a small test split across three seeds, 0.55 is a fair floor compared to 0.65 for our larger labels.

---

## 4. No single label makes up more than 45% of `labels.csv`, and every label has at least 50 examples.

<!-- Your criterion. -->

**Why this target:**
On `r/soccer`, emotional `hot_take` posts naturally dominate match threads, so capping the largest label at 45% (well below the 70% project limit) stops the model from lazily guessing `hot_take` every time. Requiring at least 50 examples per label ensures that even our smallest class gets at least 8 to 10 examples in the test split.

---

## 5. Human agreement rate on the 30-post staff evaluation set in Unit 6 is at least 70% (21 out of 30 posts).

<!-- Your criterion. -->

**Why this target:**
Internet comments often mix sarcasm with real opinions, so two people rarely agree 100% of the time. Agreeing on at least 21 out of 30 posts proves I applied written boundary rules consistently rather than guessing based on mood.

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