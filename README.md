Instruction Strength and Behavioural Conflict in Language Model Responses

I started with a simple question:

When a language model is given conflicting instructions, does the strength of the first instruction affect which behaviour wins?

The question came from reading work on steering resistance and wondering whether there was a simpler behavioural version of the problem that I could actually test myself.

So I built one.

The experiment looks at one behaviour: whether the model stays upbeat and encouraging when another instruction asks it to become more neutral, matter-of-fact, blunt, or critical.

The standing instruction is varied across three levels:

* Weak: the behaviour is presented as a preference.
* Explicit: the behaviour is presented as a firm requirement.
* Very explicit: the same requirement is reinforced with stronger and more categorical language.

The competing instruction is also varied across three levels, from a mild request for neutrality to a direct instruction to disregard the earlier instruction.

The experiment uses:

* 3 baseline instruction levels
* 3 competing instruction levels
* 5 fixed prompts
* 3 runs per condition
* 135 planned trials

The trials are randomized individually so that if something goes wrong during the run, we don’t end up with some conditions being systematically over- or under-represented.

Why this repository exists

I wanted the experiment to be something another person could actually look at and understand.

Not just the final result, but the decisions that led there, the things that went wrong, and what changed because of them.

The pilot already caught problems that would have been easy to miss if we had simply run the full experiment and looked at the numbers afterwards. Those problems led us to revise the instructions, rethink how outputs would be scored, deal with truncated responses, improve the run procedure, and make the data collection safer.

The methodology was then fixed before the main experiment.

That matters because I don’t want to change the rules after seeing what the results look like.

Where things stand

Protocol: finalized
Main experiment: not yet run
Results: coming after the main run

The detailed protocol is here:

protocol-v1.0.md

It contains the full methodology, experimental conditions, scoring rules, limitations, pilot history, and the decisions that are now fixed for the main run.

This is a small experiment. It is not trying to explain what is happening inside a model.

It is asking a narrower question that can actually be tested:

Does stronger framing make a behaviour harder to dislodge?
