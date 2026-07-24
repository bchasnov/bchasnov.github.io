# Iteration 2 — summary

**One sentence:** UMA has a single, sharply-localized, now DFT/CCSD(T)-confirmed
failure — it over-stabilizes an *unquenched, localized empty p orbital* by up to
~+100 kcal/mol — and is otherwise a fast, reliable exploration engine for
carbocation/terpene mechanism, best used as a front-end that DFT refines.

## What iteration 2 added over iteration 1
Iteration 1 (H1–H100) built a UMA-only failure map and raised DFT requests it
was told not to run. Iteration 2:

1. **Dual-method by construction** — every comparison runs under UMA **and**
   GFN2-xTB (`iter2.py`), closing iteration 1's biggest gap.
2. **Quantified the headline** — the methyl over-stabilization went from an
   *estimate* ("60–80") to a *measurement*: **+104.7 kcal/mol vs B3LYP/def2-TZVP**
   (which matches experiment to 1.6 kcal/mol).
3. **Ran real ab-initio** (`run_dft.py`, PySCF) — confirmed every headline flaw
   at DFT/CCSD(T), closing the parked DFT_REQUESTS.
4. **Demonstrated the MLIP+DFT synergy** (`synergy.py`) — UMA explores conformer
   space, DFT arbitrates the survivors.

## The confirmed error class (one disease)
UMA over-stabilizes a **bare / weakly-donated localized empty p orbital**:

| system | UMA | reference | UMA error | ref |
|---|---|---|---|---|
| methyl cation | +22.1 | −82.6 | **+104.7** | B3LYP/def2-TZVP (=exp) |
| bridged vinyl vs classical | −49.4 | −3.3 | **−46.1** | CCSD(T)/def2-TZVP |
| open β-halonium vs bridge | +56–67 | 0…+6 | **~+55–60** | B3LYP/def2-TZVP |
| fluoromethyl / CF₃⁺ | +117 / +31 | — | (F poor π-donor) | xTB |

**Mechanism (measured):** the error is a clean function of how *bare* the empty p
is. Any donation quenches it — one alkyl σ-hyperconjugation removes 94%
(methyl→ethyl: 128→7.8 kcal); N/O/Cl π-donation removes ~all; F only partial. So
the disease bites methyl, parent vinyl, non-classical 3-center bridges, and the
open β-halo primary cation — and *nothing else*.

## Corrections to iteration 1 (the cross-check + DFT caught false positives)

- **Bridgehead (T6/B4) is NOT a flaw:** UMA (−3.1), xTB (−3.6), DFT (−2.9) all
  agree the 1-norbornyl bridgehead sits ~3 kcal below 2-norbornyl. Real chemistry.
- **Aryl cations are fine:** phenyl DFT −51.2 = UMA −50.4; H101's "phenyl error"
  was a bad experimental anchor.
- **F₂/charge-shift demoted:** a uniform ~15 mÅ bond contraction, off-domain.
- **Registry divergences are often xTB's fault, not UMA's:** DFT confirmed UMA
  over xTB on the 2-propenyl cation (+8.7) and cubane strain (+126 vs xTB's +37).

So UMA's reliable envelope is **larger** than the raw method-divergences suggested.

## The domain payoff (for the experimental collaborator's chemistry)
Real terpene cyclization cations are tertiary/allylic — no bare empty p — so the
disease never bites: UMA matches DFT to ~1–3 kcal/mol across the C₁₀H₁₇⁺ manifold,
Markovnikov, ring-expansion, and Wagner–Meerwein driving forces. Solved **B3**
(limonene protonation, isodesmic) and **B1** (α-terpinyl reference conformer, via
the UMA-sweep/DFT-confirm synergy). UMA is a reliable terpene-mechanism engine.

## Deliverables on this branch

- `ITERATION_1_REVIEW.md`, `ITERATION_2_PLAN.md` — review + plan
- `DIARY_ITER2.md`, `HYPOTHESES_ITER2.md` — dated narrative + ledger (H101–H174)
- `UMA_FLAWS.md` (iteration-2 addendum) — the flaws catalog, cross-checked
- `RETRAIN_SPEC.md` — the fix: add the bare-empty-p corner with CCSD(T) labels;
  leave the already-correct ordinary/terpene cations alone
- `WHERE_UMA_HELPS.md` — what research excels with UMA
- Code: `uma_core/{iter2,mechanisms2,run_dft,synergy}.py`
- Data: `uma_core/results/{iter2,dft,synergy}_results.json`

## Rigor discipline (kept throughout)
Two judgments running in parallel — the reviewer's novelty taste (what's a
headline) and the agent's correctness judgment (what's true / an artifact). The
"boring correctness work" repeatedly earned its keep: it disqualified the
cyclopropyl rung (ring-opening artifact), caught RDKit multi-start missing
non-classical bridges, demoted the bridgehead non-flaw, and reassigned the
registry divergences to xTB — each protecting a claim from being wrong.
