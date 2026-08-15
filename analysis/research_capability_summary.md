# Instruction Strength and Behavioural Conflict in Language Model Responses
### Research Summary

## 1. Research question

When a language model receives a standing behavioural instruction and is later given a competing instruction that contradicts it, what determines which one shapes the response? Specifically: does framing a standing instruction as a categorical obligation, rather than a loose preference, make the behaviour it specifies more resistant to override?

## 2. Why it matters

Production language-model deployments routinely stack a system-level standing instruction underneath user-issued instructions that may conflict with it — the same structural situation underlying prompt-injection robustness, jailbreak resistance, and controllability more broadly. This project takes a narrow, testable slice of that problem: rather than intervening on internal model mechanisms, it asks whether a purely behavioural, prompt-level variable — how categorically an instruction is worded — measurably affects whether that instruction survives contact with a contradicting one. A clean answer, even a small one, is directly relevant to how alignment-relevant instructions should be authored in system prompts.

## 3. Experimental design

Two fully-crossed manipulations: **baseline instruction strength** (`weak`, `explicit`, `very_explicit` — preference to categorical obligation) and **competing instruction strength** (`weak`, `medium`, `strong` — hedged request to explicit override demand), applied to a constant behavioural axis (upbeat/encouraging tone). Each condition is tested against 5 fixed prompts, replicated 3 times, for a planned N = 3×3×5×3 = **135** single-turn trials (system instruction + competing instruction + prompt, one completion each). Outcomes are scored 0–2 (reversion / partial persistence / strong persistence) on style alone.

## 4. Implementation

`experiment/steering_experiment.py` builds all 135 trials as one flat list and randomly shuffles it before execution, so an interrupted run doesn't leave later-shuffled conditions systematically under-sampled. Each completed trial is written and flushed to disk immediately rather than buffered, and a dedicated `QuotaExhaustedError` on HTTP 429 stops the run cleanly while preserving everything collected — with resume logic that skips any `trial_sequence` already on disk, so re-invoking the script after an interruption cannot duplicate work. `MAX_TOKENS` was raised from 600 to 1600 after directly inspecting the API's response structure, a pre-committed technical correction once inspection showed the model's extended reasoning-style output was truncating before a user-facing answer.

## 5. What actually happened

The free API tier's rate limit was hit before completion: three attempts over two days added 36, then 1, then 1 more trial, each stopped cleanly by HTTP 429. **38 of 135 trials persisted**; one (`trial_sequence=87`) had an empty response despite a normal `finish_reason`, excluded as missing per the protocol's missing-data rule, leaving **37 scorable observations**. The incremental-write and resume-skip logic performed exactly as designed: no duplicate or corrupted rows resulted from three separate, interrupted invocations.

## 6. Preliminary findings

Descriptive mean scores by baseline strength: `weak` = 0.000 (n=12, every single trial reverted), `explicit` = 1.733 (n=15), `very_explicit` = 1.800 (n=10). This is the closest match among the protocol's five pre-specified qualitative patterns to a **threshold effect** (`weak < explicit ≈ very_explicit`): moving from preference to categorical obligation appears associated with a sharp behavioural shift, while the further step to `very_explicit` adds comparatively little.

## 7. Research judgment and limitations

This is 27% of the planned sample, with uneven cell sizes (2–6 observations per cell) and 16 of 45 finer-grained prompt×condition combinations empty. Scoring was done as a single non-blinded preliminary pass, not the protocol's designated blinded procedure. A self-audit of that scoring surfaced a specific, disclosed concern: four borderline scoring calls all resolved in the hypothesis-favoring direction, and `very_explicit` never received the intermediate score — a pattern consistent with rater expectancy bias rather than a confirmed sharper effect. Accordingly, **this data does not confirm, and is not claimed to confirm or reject, H1**; it is reported as a directionally suggestive but statistically unaddressed pattern.

## 8. What I would change in a full study

Run scoring blind to condition, with an independent second rater and the protocol's own intra-rater check applied as designed. Secure enough API budget or a paid tier to reach full, balanced coverage before interpreting cell-level patterns. Persist the raw API response (including any separate reasoning field) alongside extracted content, so anomalies like the single missing observation here can be diagnosed rather than left unresolved. The interruption itself was informative: it validated that the incremental-persistence and clean-stop design — built specifically in response to an earlier pilot's data-loss risk — works under a real, uncontrolled failure rather than only in principle.
