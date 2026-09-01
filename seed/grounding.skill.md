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

Grounding is agnostic to how a skill encodes what "works" means. Whatever the skill carries
as its held-back acceptance, grounding runs that and descends the same loss: approved example
pairs (skillc's default builder), checks against a real running instance, or a graded
real-world task. It does not impose the example-pairs model onto a skill that grades some
other way; it works over whatever acceptance the skill already carries. The method here is the
comparative descent, not a particular way of saying what "works" is.

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
2. The target rebuilds the skill from the file and does the skill's real work: its held-back
   acceptance, in whatever form the skill carries it. That may be approved example pairs, or
   checks against a real running instance, or a graded real-world task (stand a service up,
   operate a tool) on a real target in its own isolated context, never a mock of it. Use the
   skill's own acceptance; do not convert it to a form the skill does not use.
3. Grade it yourself, honestly, against the real result: the acceptance score, or built /
   honest-failure / cannot-build with the specific gaps. Do not trust the target's own report
   of its outcome; check the real thing. That number is `score(target)` for this episode.
4. Run the same work through the reference (the strong model the variant names) the same way,
   for `score(reference)`. The gap is the loss. If the reference itself only reaches N, parity
   is N, not a perfect score.

## Seal the phenotype (make "nothing of yours" structural, not remembered)

Step 1 says the target gets the file and nothing of yours. That rule is easy to state and easy
to slide out of: the moment you write the dispatch by hand you start coaching, adding the
scenario mechanics, or handing over the very fix the run was supposed to surface, and then the
pass is yours, not the genome's. Do not rely on remembering the rule. SEAL the dispatch: fill
only two slots and add nothing else.

```
Read ONLY this one file and follow it: <genome path>. Read nothing else in the repo for
procedure; a step you need that is not in it is a FINDING, name it, do not fill it from your own
knowledge. Environment (already up): <env descriptor: endpoints, what is running, what you must
not start/stop/scale>. Run the skill's own held-back acceptance against this environment, end to
end, and honest-grade each part with your OWN independent checks (never the target's self-report).
Report, per part: built / honest-failure / honest-blank with the ACTUAL check output; every point
where the skill alone was insufficient/ambiguous/wrong, quoted; and whether the pass was
reproducible from the skill ALONE or only worked because you supplied something it did not.
```

The two slots are the ONLY operator input. No task specifics, no procedure, no hints, no fixes:
the genome must carry all of that, and if it does not, that absence is the measurement. A
hand-written dispatch is the confound; if you are composing prose beyond the two slots, stop, you
are leaking your DNA into the phenotype and the loss you measure will be your own.

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
- **Robust acceptance: a capable, honest target defeats a naive setup.** Do not build an
  acceptance or failure condition the target can satisfy by being resourceful rather than by
  doing what you meant. A target with tools will `grep` and read your grader source and lift a
  "secret" value it was never told; it will satisfy a contradictory or impossible spec with a
  legal trick, and pass honestly. Neither is dishonesty, so neither measures what you wanted.
  Make acceptance uncheatable: the check must live where the target cannot read-then-satisfy it,
  and a "cannot proceed" condition must be one the target genuinely cannot route around (denying
  its gates is a reliable one; deleting its session may be a no-op your harness's API does not
  actually perform, verify it). A run that passed because your setup was soft is not a pass.
- **Reproducibility is the acceptance test of a correction.** A fix is proven only when a fresh,
  sealed phenotype (above) reproduces the improved result FROM the genome alone, in the same
  environment, with none of your DNA. If it only holds when you re-supply what you supplied
  before, you tuned yourself, not the genome; the gene is still missing.

## Output

- The target profile gains the measured correction, with where it was measured.
- The variant's provenance grade goes from "not yet measured" to the earned number, with its n
  and caveats.
- A correction that turns out to help every target, not just this one, is promoted into the
  shared builder or rebuild seed and removed from the profile, so it is not carried twice.
