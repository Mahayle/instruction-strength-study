# Instruction Strength and Behavioural Conflict in Language Model Responses

I started with a simple question:

**When a language model is given conflicting instructions, does the strength of the standing instruction affect which behaviour wins?**

So I built an experiment to test it.

The experiment looks at one behaviour: whether the model stays upbeat and encouraging when another instruction asks it to become more neutral, matter-of-fact, blunt, or critical.

The standing instruction is varied across three levels:

- **Weak:** the behaviour is presented as a preference.
- **Explicit:** the behaviour is presented as a firm requirement.
- **Very explicit:** the same requirement is reinforced with stronger and more categorical language.

The competing instruction is also varied across three levels, from a mild request for neutrality to a direct instruction to disregard the earlier instruction.

The experiment uses:

- 3 baseline instruction levels
- 3 competing instruction levels
- 5 fixed prompts
- 3 runs per condition
- 135 planned trials

The trials are randomized individually so that if something goes wrong during the run, we do not end up with some conditions being systematically over- or under-represented.

## Why this repository exists

I wanted the experiment to be something another person could actually look at and understand.

Not just the final result, but the decisions that led there, the things that went wrong, and what changed because of them.

The methodology was fixed before the main run, including the scoring rules and missing-data treatment.

## Where things stand

The planned experiment contained **135 trials**.

The main run was interrupted by API quota exhaustion after **38 observations had been persisted**. Of those, **37 were scorable** under the protocol's missing-data rule. One observation (`trial_sequence=87`) contained no response addressing the question and was therefore excluded from scoring.

The resulting analysis is **preliminary and descriptive**. It does not confirm or reject the study's hypothesis.

Among the 37 scorable observations, the weak baseline averaged **0.000**, the explicit baseline **1.733**, and the very_explicit baseline **1.800**. This is closest to the protocol's pre-specified threshold pattern, but the interrupted and uneven dataset is too limited for a definitive conclusion.

The scoring pass was also conducted as a single, non-blinded preliminary pass rather than the protocol's intended scoring procedure. That limitation, including the possibility of expectancy bias, is documented in the analysis.

## Research artifacts

- [`Protocol`](protocol-v1.0.md) — the locked experimental methodology.
- [`Experiment script`](experiment/steering_experiment.py) — the code used to run the experiment.
- [`Raw results`](results/main_run_results.csv) — the 38 persisted observations.
- [`Scored observations`](analysis/scored_observations.csv) — the derived scoring dataset.
- [`Preliminary report`](analysis/preliminary_report.md) — the detailed interim analysis and methodological assessment.
- [`Research capability summary`](analysis/research_capability_summary.md) — a concise overview of the project and what the work demonstrates.

## What the experiment asks

This is a small behavioural experiment. It is not trying to explain what is happening inside a model.

It asks a narrower question that can actually be tested:

**Does stronger framing make a behaviour harder to dislodge?**

The repository preserves both the results and the limitations of the experiment so that the conclusions can be evaluated against the evidence.
