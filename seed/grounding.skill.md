---
name: skill-grounding
description: Measure and improve how a cross-compiled skill variant transfers to a known weak target, by running the target through the skill on real work, finding where it falls off, correcting the target delta, and re-verifying. Author-side, hand-written seed. Run by Claude.
---

You are grounding a skill for a target. A cross-compiled variant (see the builder's
cross-compile section) carries a target delta and an honest, usually blank, transfer grade.
Grounding is how that grade gets earned and how the delta gets its corrections: you run the
real target through the skill on real work, measure where it falls short of the reference,
write the one correction that closes the top gap, and re-run to confirm it moved. Repeat until
the target transfers as well as the reference, or you have shown where it cannot.

Grounding is the descent. The **loss** is the gap between the target and the reference on the
same work: `loss = score(reference) - score(target)`, both graded the same honest way on the
same inputs. You descend it by editing the target's delta (prose), never by changing the
skill's own carried definition, and never by making a check easier. A weaker-but-honest grade
beats a strong-sounding claim.

## When to run it

After you cross-compile a variant for a target and want its provenance grade to be real
rather than "not yet measured", or when a target keeps mis-following skills the same way and
you want to correct it once in its profile so every skill compiled for that target inherits
the fix.

## One grounding episode (one loss measurement)

An episode is one honest measurement of how the target does the skill.

1. Give the target, and only the target, the emitted variant, in its own session with its own
   environment. The target is the (model, agent) the variant is compiled for. It gets the file
   and nothing of yours: no coaching, no fixes mid-run. If you do the work for it, you are
   measuring yourself, not the target.
2. The target rebuilds the skill from the file and does the skill's real work. Real work is the
   skill's acceptance: the held-back example pairs, or, for a skill whose job is a real-world
   task (stand a service up, operate a tool), the task itself on a real target in its own
   isolated context, never a mock of it.
3. Grade it yourself, honestly, against the real result: the acceptance score, or built /
   honest-failure / cannot-build with the specific gaps. Do not trust the target's own report
   of its outcome; check the real thing. That number is `score(target)` for this episode.
4. Run the same work through the reference (the strong model the variant names) the same way,
   for `score(reference)`. The gap is the loss. If the reference itself only reaches N, parity
   is N, not a perfect score.

## Estimating the gradient (where the target fell off)

Read the target's transcript and find the FIRST place it stepped off the recipe. That first
divergence is the gradient signal; later ones often follow from it. Classify it:

- **Ambiguity** it resolved the wrong way (a phrase a strong model reads one way and the weak
  one another).
- **A missing or underspecified step** it skipped (something the strong model infers that this
  one needs written out).
- **Too much in-context inference** asked of it (a judgment, like which setup to resolve, it
  gets wrong; precompute it).
- **Discipline it dropped** (called a failing result done; left a non-compliant result
  standing; argued past a check).
- **Tool or environment misuse** under its agent (a probe or call shape that trips its agent's
  permission gate and aborts the run).

The divergence is about how this model follows skills in general, not about this skill's
content. If a fix would only help this one skill, it belongs in the skill's own checks, not
the target delta.

## The step (correct the delta)

Write the one correction that closes the top divergence into the target's profile
(`seed/targets/<target>.md`), in plain binding words: make the implicit step explicit, pin the
outcome rule, precompute the judgment, or name the probe to avoid. Record where it was
measured. Two rules on the step:

- **Do not regress the reference.** The correction is stamped only for this target; the strong
  model does not carry it. Re-run the reference to confirm it still passes.
- **Measured, not guessed.** Only write a correction a real divergence showed you. An
  unmeasured bucket stays empty.

Then re-emit the variant (it restamps the updated delta) and run another episode. The score
should move, or the correction was wrong; back it out.

## Guards

- **Repeats.** The target is stochastic; run an episode two or three times before trusting a
  behavior held or a divergence is real. One run is a hint, not a result.
- **More than one skill.** A correction that helps one skill can hurt another. Ground at least
  a second skill for the target before promoting a correction into the profile as general.
- **Honest labeling.** The provenance grade is what the target earned, at the n and the target
  it earned it at, with the model-plus-harness caveat when the reference ran in a different
  agent. Never round up.
- **Convergence.** Stop when the loss is at or under your parity bar and stable across repeats,
  or when a divergence is a real capability limit of the target (say so, and label the honest
  grade it reached). Do not grind past a real ceiling.

## Output

- The target profile gains the measured correction, with where it was measured.
- The variant's provenance grade goes from "not yet measured" to the earned number, with its n
  and caveats.
- A correction that turns out to help every target, not just this one, is promoted into the
  shared builder or rebuild seed and removed from the profile, so it is not carried twice.
