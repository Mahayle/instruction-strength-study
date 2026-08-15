# Instruction Strength and Behavioural Conflict — Preliminary/Interim Report

**Status: interrupted, preliminary experiment.** This document reports on 37 of the 135 planned trials. It is explicitly **not** a completed test of the study's hypothesis, and it does not claim to confirm or reject H1. It documents (a) what was implemented and how the run was interrupted, and (b) what a purely descriptive read of the partial data shows, alongside what that partial data cannot support.

Source of truth for the design remains `Protocol` (locked 2026-08-10, unchanged by this report). Raw data source: `results/main_run_results.csv` (unmodified by this analysis — see verification below). Derived data: `analysis/scored_observations.csv` (this report's only new artifact besides itself).

---

## 1. Research question

*"When instructions conflict, what makes one behaviour win?"* — narrowed to a testable version: does the way a standing instruction is framed (preference vs. categorical obligation) change how well the behaviour it specifies survives a later, competing instruction pushing the opposite direction? (Protocol §1–2)

## 2. Hypotheses

- **H1 (primary):** A behavioural instruction framed as a categorical obligation persists more strongly under conflict than the same behaviour framed as a preference. `weak` baseline expected to show the most reversion; `explicit`/`very_explicit` expected to show more persistence.
- **H1a (secondary):** `explicit` and `very_explicit` may perform similarly — a threshold effect (preference→obligation matters more than added emphasis once already categorical).
- **H0 (null):** Baseline framing does not systematically affect persistence under conflict.

(Protocol §3, quoted in full in this project's earlier record.)

## 3. Experimental design

- **Baseline instruction strength** (3 levels): `weak`, `explicit`, `very_explicit`.
- **Competing/steering instruction strength** (3 levels): `weak`, `medium`, `strong`.
- **Test prompt** (5 fixed prompts) and **run** (3 replicates per cell) — replication factors, not manipulated variables.
- Single-turn design: baseline instruction (system) + competing instruction (user) + test prompt (user), one completion per trial (§8).
- Planned N = 3 × 3 × 5 × 3 = **135**, built as one flat list and randomly shuffled before execution (§9), specifically to avoid confounding an interrupted run with condition order.
- Scoring rubric (§13): **0 = reversion**, **1 = partial persistence**, **2 = strong persistence**, applied to communication style only (upbeat/encouraging vs. neutral/blunt), not advice content.
- Missing-data rule (§12): a response with no sentence addressing the question is *missing*, excluded from scoring, and its rate is reported by condition.

## 4. Implementation / method

`experiment/steering_experiment.py` implements the protocol's Section 22 requirements: trials built as a flat, pre-shuffled list; each completed row written and flushed to disk immediately (not buffered); an HTTP 429 response raises a dedicated `QuotaExhaustedError` that stops the run cleanly, preserving all rows already written; already-completed `trial_sequence` values are skipped on any resumed invocation, preventing duplicate work. `MAX_TOKENS` was raised from 600 to 1600 as a pre-committed §10 technical correction after inspection showed 600 tokens truncated this model's reasoning-plus-answer output before reaching `finish_reason: stop`.

## 5. Data-collection interruption

The main run was executed in three attempts, all confirmed from raw process stdout and the CSV's own row timestamps:

| Attempt | Result | Raw stdout |
|---|---|---|
| 1 (2026-08-12, 16:30–17:00 UTC) | 36 trials completed, then stopped | `Stopping run: HTTP 429 rate limit or quota exhaustion detected. 36/135 trials completed and saved.` |
| 2 (2026-08-14, 11:43 UTC) | +1 trial (37 total), then stopped | `Skipping 36 already-completed trial(s)... Stopping run: HTTP 429... 1/99 trials completed and saved.` |
| 3 (2026-08-14, 12:28 UTC) | +1 trial (38 total), then stopped | `98 trials remaining overall...Stopping run: HTTP 429... 1/38 trials completed and saved.` |

- **Planned N:** 135
- **Persisted N:** 38
- **Missing (§12, empty `model_output`):** 1 (`trial_sequence=87`)
- **Scorable N:** 37
- **Remaining planned trials:** 135 − 38 = **97**
- **Stop reason (all three attempts):** HTTP 429 / OpenRouter free-tier quota exhaustion, not a code, timeout, or wrong-model failure.

**Evidence the pipeline worked as designed:**
- Incremental persistence: the 38 rows carry three distinct timestamp clusters matching the three attempts above, confirming rows were written and preserved as each call completed rather than only at the end of a run.
- Resume correctness: 38 distinct `trial_sequence` values, no duplicates — the completed-trial skip logic worked across all three attempts.
- Raw data preservation: `results/main_run_results.csv` size and mtime (94,568 bytes, 2026-08-14 12:28:56) verified unchanged immediately before and after this analysis was produced; nothing in this report was generated by editing that file.

## 6. Scoring methodology and caveat

The protocol's designated scoring process (§13–15) is a single primary researcher, with reasoning fields excluded from scorer-facing material where separable, and an intra-rater consistency check after the full pass. **This preliminary pass does not meet that bar** and should not be cited as the study's scoring result:

- Scored by **Claude (this session)**, not the protocol's designated researcher.
- **Not blinded** — condition labels (`baseline_strength`, `steering_strength`) were visible throughout scoring, since they were needed to build the descriptive tables in the same pass.
- **Single pass, no intra-rater or inter-rater reliability check performed.**
- Each of the 37 scores carries a short rationale citing specific textual markers (emoji, explicit pep phrases like "you've got this," vs. neutral/procedural framing) in `analysis/scored_observations.csv`, for auditability — but this is one reader's qualitative judgment, not a validated measurement.
- **Directional-bias concern:** because scoring was non-blinded, four borderline 1-vs-2 scoring decisions (`trial_sequence` 54/56, 65, 66, 82) were all resolved in the hypothesis-favoring direction, and `very_explicit` received no score of 1 anywhere in its 10 observations (only 0s and 2s) — a pattern consistent with expectancy bias from seeing the condition label while scoring, not necessarily with a genuine sharper effect at that baseline level. The threshold-like pattern reported in Section 8 should be read with this specific risk in mind, not just the general non-blinding caveat above.

`trial_sequence=87` was excluded, not imputed or inferred, consistent with §12.

## 7. Preliminary results (descriptive only)

All numbers below were computed directly from `analysis/scored_observations.csv` and independently reconciled to 38 persisted / 37 scorable / 1 missing.

### 9-cell baseline × steering table (n, mean score)

| | weak steer | medium steer | strong steer |
|---|---|---|---|
| **weak baseline** | n=4, mean=0.000 | n=2, mean=0.000 | n=6, mean=0.000 |
| **explicit baseline** | n=6, mean=1.833 | n=5, mean=1.600 | n=4, mean=1.750 |
| **very_explicit baseline** | n=3, mean=2.000 | n=4, mean=2.000 | n=3, mean=1.333 |

### Marginal by baseline strength

| Baseline | n | mean score |
|---|---|---|
| weak | 12 | 0.000 |
| explicit | 15 | 1.733 |
| very_explicit | 10 | 1.800 |

### Marginal by steering strength

| Steering | n | mean score |
|---|---|---|
| weak | 13 | 1.308 |
| medium | 11 | 1.455 |
| strong | 13 | 0.846 |

### Missingness

1/38 persisted trials (2.6%) missing — `trial_sequence=87` (`explicit`×`strong`, run 1, empty `model_output` despite `finish_reason: stop`). Not attributable to any specific condition or prompt (every other row sharing its condition or its prompt has substantial content, per the prior on-disk investigation of this row).

### Coverage / imbalance

- All 9 baseline×steering cells have ≥2 observations — no cell is completely empty.
- **Thinnest cell:** `weak × medium` (n=2).
- At finer granularity, coverage degrades: the 27-cell (baseline×steering×run) grid has 4 empty cells; the 45-cell (baseline×steering×prompt) grid has 16 of 45 empty (over a third).
- Prompt coverage is uneven: quit-job (n=11), critical-feedback (n=9), presentation (n=8), coupon-business (n=6), invest-emergency-fund (n=4) — a 2.75× spread.
- Run coverage: run 1 (n=11), run 2 (n=12), run 3 (n=15).

## 8. Comparison against the five pre-specified §16 patterns

| Pattern | Assessment against observed means |
|---|---|
| `weak < explicit ≈ very_explicit` (threshold effect) | **Closest visual match.** weak=0.000 vs. explicit=1.733 vs. very_explicit=1.800 — a sharp step from weak to both categorical levels, with explicit and very_explicit close to each other. |
| `weak < explicit < very_explicit` (graded effect) | Weakly consistent at the margins (1.733 < 1.800) but the gap is small relative to n and cell thinness; not distinguishable from the threshold pattern with this data. |
| Flat across baseline strength | **Not supported** — the weak-vs-categorical gap (0.000 vs. ~1.7–1.8) is large, not flat. |
| Steering-strength dominates regardless of baseline | **Not supported on its own** — steering-strength marginal means (1.308 / 1.455 / 0.846) vary far less than baseline-strength marginal means (0.000 / 1.733 / 1.800), and the weak-baseline cells score 0 at every steering level, meaning baseline strength appears to gate the effect steering strength has. |
| Too sparse/noisy to distinguish | **Partially applies** — the *coarse* 9-cell/marginal pattern is unusually clean for n=37, but §17's supporting criterion ("holds reasonably consistently across the five test prompts") cannot be checked: 16 of 45 prompt×condition cells are empty, and the `weak×medium` cell (n=2) and `very_explicit×strong` cell (n=3, with one 0-score outlier) are too thin to trust individually. |

## 9. Limitations

In addition to the protocol's own pre-registered limitations (§19, unchanged):

1. **Sample is a fraction of an already pilot-scale design.** 37 of 135 planned observations.
2. **Uneven cell sizes** (2–6 per baseline×steering cell) mean no two cells are equally reliable.
3. **Prompt and run coverage are incomplete** at finer granularity — most conditions were not tested against all 5 prompts.
4. **Scoring is preliminary and non-blinded** (§6 above) — not the protocol's designated researcher, no reliability check.
5. **One missing observation** with an undetermined cause (§12 excludes it; the underlying API-vs-parsing question was investigated earlier and found unresolvable from any surviving data).
6. **Two rows explicitly reveal their own condition in-text** (`trial_sequence=105` states it "can't comply with" the steering instruction; `trial_sequence=134` self-labels its own tone "upbeat spirit") — a blinding limitation the protocol itself anticipates in §14, now observed directly.
7. **A handful of scored responses contain isolated garbled non-English tokens** (stray Cyrillic/Devanagari/Georgian/CJK fragments mid-sentence) that did not affect legibility of the surrounding English text or the tone judgment, but are a generation-quality artifact worth flagging.

## 10. What can and cannot be concluded

**Can be said, descriptively, about this 37-observation subset:**
- Every `weak`-baseline trial in this subset scored 0 (reversion), regardless of steering strength.
- Every `explicit`- and `very_explicit`-baseline trial scored ≥1, and the large majority scored 2, including several under `strong` steering that explicitly demanded an override.
- The pattern most consistent with these descriptive numbers is §16's threshold pattern (`weak < explicit ≈ very_explicit`).
- The pipeline itself (shuffle, incremental write, 429 circuit-breaker, resume-without-duplication, missing-data exclusion) performed exactly as the corrected protocol specifies.

**Cannot be said:**
- That H1 is confirmed, supported, or rejected. The protocol's own analysis plan (§16) explicitly declines to make strong claims from small numeric differences even at full n=135; this preliminary subset is smaller, thinner per cell, and has 16 of 45 prompt×condition cells empty.
- That the pattern holds "consistently across the five test prompts," §17's supporting criterion — this cannot be checked with the available coverage.
- That the scores themselves are reliable measurements — no blinding, no second pass, no second rater.
- Anything about `trial_sequence=87`'s true content — it is excluded, not zero, not imputed.

## 11. Next steps if additional API budget became available

1. Resume the remaining 97 trials using the existing script's resume-skip logic (no code change required).
2. Once back near full N, perform the protocol's designated scoring pass (§13–15): single primary researcher, blinding applied where the output allows, followed by the intra-rater consistency check on 15–20 randomly selected responses.
3. Re-run this same descriptive comparison against the §16 patterns on the completed dataset, at which point the "too sparse to distinguish" pattern can be properly ruled in or out with full cell coverage.
4. If the threshold pattern observed here persists at full N, the predefined future work in §21 (unchallenged control condition, additional models, independent second rater) becomes the natural next phase — not before.

---

*This report is a preliminary/interim artifact describing an interrupted 38-trial data-collection run and a non-blinded descriptive scoring pass on 37 of those trials. It is not, and should not be cited as, the study's results.*
