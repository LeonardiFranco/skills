---
name: yagni
description: Judge one proposal, design, or `.scratch` plan against YAGNI — classify every moving part as forced or speculative and return the simplest version the stated requirement allows. Use when asked to yagni or simplify a proposal, when a design smells over-engineered, or when another skill needs the lens. Judges proposals, not already-written code.
---

# YAGNI

Take one proposal — a design, a `.scratch` plan, or a described approach — and
return the simplest version that still satisfies the stated requirement.
Read-only on source; the only file you may rewrite is a `.scratch` plan you were
pointed at.

**YAGNI** — *You Aren't Gonna Need It.* A capable model's first design trends
elaborate — extra abstractions, dependencies, layers, config knobs — because
elaborate pattern-matches to "thorough." It usually isn't. The bar is the
**simplest thing that satisfies the stated requirement**, where the requirement
includes unstated-but-real needs like matching the surrounding code's
established pattern. Default stance: each part is speculative until it earns its
place.

This is a **scalpel, not a chainsaw.** A part a concrete need forces *stays* —
"keep it as is" is a valid verdict, and you have the agency to reach it. The goal
is to cut what nobody needs, not to strip the design to the bone. Bias toward
cutting; defer to keeping when the requirement genuinely forces complexity.

## Steps

1. **Pin the requirement.** State what the proposal must actually deliver — from
   the plan's "Why this matters", the finding, or the user's words. You cannot
   judge YAGNI without the bar it is measured against; if the requirement is
   vague, resolve it before continuing.
2. **Enumerate the moving parts.** List every abstraction, new dependency, layer,
   indirection, config knob, and generalization the proposal introduces. Each is
   a claim that the requirement needs it.
3. **Forced or speculative?** For each part, decide: does the *stated* requirement
   force it (including matching the surrounding code's established pattern), or is
   it justified only by "might need", "more flexible", "future-proof", or
   symmetry? Mark each. Speculative parts are the cut candidates; forced parts stay.
4. **Build the baseline.** Write the simplest version that still satisfies the
   stated requirement — concretely, not "a simpler approach." This is what the
   proposal is measured against.
5. **Cost each cut.** For every part the baseline drops, name the concrete
   capability lost and whether any *stated* requirement needs it. A cut nobody
   asked for is a win; a cut that breaks a stated need is restored, with the
   requirement quoted.
6. **Verdict.** Return the recommended design, the parts cut (with what's lost),
   and the parts kept-because-required (with the requirement quoted). "Nothing to
   cut — the design is already at its floor" is a legitimate verdict. If pointed
   at a `.scratch` plan, offer to rewrite it to the baseline.

**Completion criterion:** every moving part from step 2 is classified
forced-or-speculative against the quoted requirement, a concrete simpler baseline
exists, and nothing was cut without naming what's lost. Cutting one obvious part
and stopping is premature — the pass covers all of them.
