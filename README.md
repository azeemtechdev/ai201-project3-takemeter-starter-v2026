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

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

This classifier sorts user comments from `r/soccer` (Reddit match threads and daily discussions) into three clear categories: `tactical_analysis`, `hot_take`, and `banter`. Online football forums are flooded with thousands of emotional reactions and jokes after every match, which buries thoughtful breakdowns. TakeMeter separates actual tactical observations from angry venting and casual jokes.

---

## Label Taxonomy

### `tactical_analysis`

**Definition:** The post explains match events, player performance, or coaching decisions by pointing to specific formations, pitch positioning, phases of play, or match stats.

**Example 1:**
> "They shifted into an asymmetrical back three in possession, letting the right wingback push up to pin the fullback while the double pivot covered transition spaces."

**Example 2:**
> "He completed only 68% of his forward passes today because the opposition mid-block cut off central passing lanes and forced him into contested wide balls."

### `hot_take`

**Definition:** The post makes an extreme claim, emotional complaint, or bold prediction about a player, club, or manager without giving any stats or tactical reasons.

**Example 1:**
> "Sell him in January, he is completely washed and has never cared about playing for this badge."

**Example 2:**
> "This manager is a fraudulent coward who will have us fighting relegation by Christmas."

### `banter`

**Definition:** The post is a joke, a meme, sarcastic mockery, or a short one-liner with no serious argument.

**Example 1:**
> "Football heritage. We are so back."

**Example 2:**
> "Can he do it on a cold rainy night in Stoke though?"

### The hardest boundary

**Which two labels:** `tactical_analysis` vs. `hot_take`

**The decision rule I used every time:**
If a post mentions a specific checkable stat (like xG or pass completion) or a specific tactical action on the pitch (like pressing, high defensive line, or passing lanes), it is `tactical_analysis`—even if the person sounds angry. If the post just insults a player or manager without saying *how* or *why* tactically, it is `hot_take`.
---

## The Dataset

**Where the posts came from:** Public post-match threads and daily discussion threads on `r/soccer` (Reddit).

**How I labelled them:** I labelled the first 20 posts cold by hand using only my written definitions and decision rule (marked `cold` in the `note` column), then used AI pre-labelling for the remaining 180 posts and manually reviewed and verified every row against the Mechanism Rule.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `tactical_analysis` | 64 | 32.0% |
| `hot_take` | 78 | 39.0% |
| `banter` | 58 | 29.0% |
| **Total** | **200** | **100%** |

**Three hard cases**

**1.**
> *The post:* "Midfield was completely invisible for 90 minutes. Zero tracking back from the wingers leaving the fullbacks isolated against overloads."
>
> *Could have been:* `hot_take` or `tactical_analysis`
>
> *I chose, because:* `tactical_analysis`. Although "completely invisible" is hyperbolic venting, the second sentence identifies the specific tactical breakdown: lack of defensive tracking creating wide 2v1 overloads.

**2.**
> *The post:* "Tiki-taka into walking the ball into the net while down 2-0. Masterclass in possession football."
>
> *Could have been:* `tactical_analysis` or `banter`
>
> *I chose, because:* `banter`. While it uses tactical terms ("tiki-taka", "possession football"), the phrasing is pure sarcasm and rhetorical mocking ("masterclass") intended as forum humor rather than an earnest assessment of the passing structure.

**3.**
> *The post:* "0.12 xG from 14 shots."
>
> *Could have been:* `tactical_analysis` or `hot_take`
>
> *I chose, because:* `tactical_analysis`. The post contains zero subjective words or editorial tone; it conveys an objective, recorded statistical metric reflecting poor shot selection quality.

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Settings:** Epochs: 3, Learning Rate: 2e-5, Batch Size: 16, Random Seed: 42

**Anything I changed from the defaults, and why:** Kept the default hyperparameters provided in the starter notebook. With a 200-example dataset, 3 epochs at a 2e-5 learning rate prevents catastrophic forgetting of the pre-trained weights while providing sufficient gradient updates to separate the three labels.

**Split sizes:** 
* Train: 140 examples (70%) / Val: 30 examples (15%) / Test: 30 examples (15% held-out stratified split)
  * Test split counts: `tactical_analysis`: 10, `hot_take`: 11, `banter`: 9.
  * Every label has at least 9 examples in the test split, keeping all three classes above the 8-example variance floor.

---

## How I Used AI

**Moment 1**

- *What I asked for:* How to construct a reliable decision rule for distinguishing emotional match rants from actual tactical critiques on a sports forum.
- *What came back:* The suggestion to establish an "Evidence/Mechanism Rule": separating posts based on whether they describe causal mechanics (formations, movements, numbers) versus naked evaluations.
- *What I changed:* Tailored the rule specifically to football discussions on Reddit by requiring explicit mentions of tactical phases (e.g., transition, pressing triggers, overloads) or recorded stats to qualify as `tactical_analysis`, regardless of how aggressive or informal the tone is.

**Moment 2**

- *What I asked for:* How to write realistic, measurable acceptance criteria before running a classifier on only 200 text rows.
- *What came back:* Recommendations to define targets around Macro F1, class balance constraints, baseline delta over majority voting, and minority class recall.
- *What I changed:* Tuned the Macro F1 threshold to >= 0.70 and specified a minority-class recall target (>= 0.60) to prevent the classifier from ignoring the `banter` class to inflate overall accuracy.

**Pre-labelling disclosure:**
After labelling the first 20 examples cold by hand, I used an AI assistant with my three label definitions and the Mechanism Rule to pre-label the remaining 180 `r/soccer` comments, then manually inspected and verified each row and note in `labels.csv`.

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

**What the gap actually means:**

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

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**

---

## Verdicts and Diagnoses

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

---

## Agreement Report

**Agreement rate:** ___ / 30 = ___%

**Disagreements**

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

---

## What's Still Broken

**The gap between what I meant my labels to capture and what the model
learned:**