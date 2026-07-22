# Next round — driven by reviewer feedback

The first campaign (H1–H100, see `HYPOTHESES.md`) ran ~100 DFT-free hypotheses
using UMA as an instrument to explore carbocation/terpene mechanism. A domain
reviewer triaged 16 distilled findings through the feedback inbox
(`feedback/feedback_inbox.html`). Their verdicts reframed the project: **for a model
sold as general, the flaws are the story — and the payoff is knowing how to
retrain it for mechanistic natural-product chemistry.**

---

## Reviewer verdicts (2026-07-21)

| # | Finding | Reaction | Comment |
|---|---|---|---|
| B1 | α-Terpinyl cation: is the relocated global minimum real? | verify | — |
| B2 | tert-butyl → sec-butyl gap: which number do we cite? | verify | — |
| B3 | Limonene → α-terpinyl protonation: no trustworthy number | hold | — |
| B4 | 1-norbornyl bridgehead sits BELOW 2-norbornyl — by how much is UMA off? | interesting | — |
| B5 | Bridged vinyl cation 52.8 kcal/mol below classical — real or over-stabilization? | **chase** | — |
| B6 | β-chloronium/bromonium: UMA puts BRIDGED above OPEN | hold | — |
| T1 | UMA misses antiaromatic Jahn–Teller distortion (Cp⁺) | interesting | — |
| T2 | Cp⁺ triplet −200 kcal/mol below singlet (unphysical) | interesting | — |
| T3 | Dichlorocarbene: UMA predicts triplet GS (should be singlet) | interesting | — |
| T4 | Bare methyl cation over-stabilized ~60–80 kcal/mol | verify | "Check for reported results in literature, if none, make headline" |
| T5 | F₂ dissociation non-monotonic & ~4× too strong | verify | "Check other homolytic charge-shift bonds. Is this that relevant to carbocation or natural product chemistry?" |
| T6 | Bredt bridgehead cation under-destabilized | interesting | "Evidence of carbocation stabilization errors" |
| T7 | Single-start relaxation misses non-classical minima | boring | — |
| T8 | Cubane strain 166 kcal/mol — essentially exact | boring | — |
| T9 | RAHB flips acetylacetone to enol — only with multi-start | hold | — |
| T10 | β-silicon effect: C–Si bridges, +30.7 kcal/mol stronger σ-donor | hold | "Overall the flaws are more interesting than the success, especially as it is touted as a general model. We can briefly comment on it's expected successes, but should consider how it can be retrained or what molecules should be added to make it more transferable to carbocation and mechanistic natural product chemistry" |

Raw export: `feedback/verdicts_2026-07-21.json` (if saved).

---

## What the verdicts mean (decoded preferences)

- **The flaws are the paper; the successes are the setup.** (T10) Expected
  competence (cubane T8, β-Si T10, RAHB T9) bores the reviewer *because* the model
  claims generality. The failures on reactive intermediates are the contribution.
- **One thesis: carbocation stabilization errors.** (T6) The interesting-marked
  flaws cluster into a single class — UMA gets carbocation *relative* energies
  wrong: over-stabilizing small localized-empty-orbital cations (T4 methyl, B5
  vinyl → "chase") and under-penalizing geometry-constrained ones (B4/T6
  bridgehead).
- **Domain relevance is the master filter.** (T5) Off-domain flaws (F₂/charge-
  shift) are demoted even when dramatic.
- **Novelty gates "interesting."** (T4) Literature-check first; unreported → headline.
- **Open-shell/spin failures (T1/T2/T3) interest the reviewer but are off the
  domain axis** — a real secondary story, lower priority.

### Meta-principle (do not collapse my judgment into the reviewer's)
The reviewer's "boring" is a **novelty** signal (what *they* already know), **not**
a validity/importance signal — and they can't rank what the agent is good at. So
their taste **prunes what's a headline; it must not suppress what gets explored or
verified.** Keep two judgments running: theirs (novel? domain-relevant? → the
paper) and mine (true? load-bearing? artifact? → the science). The single-start
example proves it: they shrugged (known), but that same "boring" fact is what
caught halonium/vinyl/RAHB being single-start artifacts before they were falsely
reported as flaws. **Do the boring correctness work — it licenses the exciting
claims.**

---

## THE GOAL (next running loop)

> **Build the rigorous failure taxonomy of UMA on carbocation stabilization, and
> prescribe the data that would fix it.**
>
> **Primary thread — the carbocation stabilization error class.** With a *designed*
> cation series (methyl → 1°/2°/3° → allyl/benzyl → vinyl → cyclopropylcarbinyl →
> non-classical/bridged → bridgehead → terpene C₁₀H₁₇⁺), systematically map
> **where and by how much UMA over- vs under-stabilizes cations**, using
> isodesmic/isomeric UMA comparisons plus xTB cross-checks. Test the working
> hypothesis: *UMA over-stabilizes small/localized empty-orbital cations (methyl,
> vinyl) and under-penalizes cations that can't planarize/delocalize (bridgehead).*
> Chase B5 (vinyl), unify with T4 (methyl) and B4/T6 (bridgehead) into one
> coherent, quantified error class with a mechanism story.
>
> **Rigor gate on every candidate flaw (the boring work that licenses the exciting
> claims):** (1) multi-start / symmetry-break to prove it isn't a sampling
> artifact; (2) xTB cross-check; (3) literature/novelty check via web search —
> unreported → headline, known → cite. Do this even when a finding feels obvious.
>
> **Constructive output:** name the specific molecule classes and reference data
> (non-classical/bridged carbocations, bridgehead ions, terpene rearrangement
> intermediates) whose addition would make an MLIP transferable to
> carbocation/natural-product mechanism.
>
> **Secondary thread:** document the open-shell/degenerate failures (Cp⁺
> antiaromatic, spin-state/carbene) tightly as a distinct, lower-priority class.
> **Demote off-domain flaws** (F₂/charge-shift): check whether they generalize,
> judge relevance to carbocation/terpene chemistry, shelve if tangential.
>
> **Keep my curiosity independent of the reviewer's taste:** their "boring" marks
> what they already know, not what's unimportant or unverified — keep surfacing and
> verifying things like the single-start trap and let literature sort
> novel-from-known downstream. Park the DFT-dependent numbers (B1 α-terpinyl
> minimum, B2 tert/sec gap) as clearly-scoped requests, firmed up with multi-start
> + xTB + literature.
>
> **Loop discipline:** one hypothesis at a time, wrinkle-driven; validate every
> structure; multi-start always; dated diary + hypothesis ledger + flaws log;
> commit each batch.

**DFT-free by design** (UMA + xTB + literature). Produces a defensible failure map
and a retraining spec; the two "verify" numbers (B1, B2) stay open until DFT is
available — parked, not pretended-closed.

## Open questions for the reviewer
- Target venue/audience — comp-chem/MLIP methods, or physical-organic/natural
  product? (Changes what counts as a headline.)
- the reviewer DFT be available for the verify items (B1, B2, T4)? If so, spec exactly what
  to run; if not, stay DFT-free + literature.
- Confirm priority: carbocation-stabilization errors (primary) → open-shell/spin
  (secondary) → off-domain F₂ (demote).
- How far into the retraining prescription — name the gaps, or curate/spec a
  candidate augmentation set?
