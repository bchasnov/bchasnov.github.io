# Next research directions (iteration-3 seed)

Grounded in iteration 2 (`SUMMARY_ITER2.md`). Iteration 2 characterized UMA's
carbocation energetics on **minima** and confirmed one disease (bare empty p) at
DFT/CCSD(T). These are the directions the findings most sharply motivate,
ordered by leverage.

## 1. Barriers / transition states — the biggest gap (highest priority)
Iteration 2 tested only minima and isomer energetics. **Mechanism is decided by
barriers** — Wagner–Meerwein shifts, cyclizations, hydride shifts. Warning sign:
the one clean dissociation failure (F₂) was at **stretched bonds**, and a TS is a
region of partial/stretched bonding with a locally electron-deficient center —
exactly the "bare-p-like" regime UMA over-stabilizes.

- **Do:** build a barrier benchmark (1,2-H / 1,2-Me shifts, ring expansions,
  cyclizations) via NEB/string with UMA, DFT-refine the TSs, compare barriers.
- **Why it matters:** if UMA barriers are trustworthy, the UMA-explore/DFT-refine
  cascade workflow is fully general for the experimental collaborator's mechanism work; if not, that is a
  critical, unmapped MLIP limitation.

## 2. Close the retraining loop (diagnosis → cure → re-test)
`RETRAIN_SPEC.md` names the gap and now carries real DFT/CCSD(T) labels.

- **Do:** generate the small bare-empty-p augmentation set (methyl / halomethyl /
  parent vinyl / non-classical bridges / β-halonium, CCSD(T) labels), fit a cheap
  **Δ-learning residual correction** on top of UMA, and re-run the iteration-2
  ladders (`mechanisms2.py`) to verify the methyl/vinyl/halonium errors collapse
  while terpene/ordinary numbers stay put.

## 3. Predictive error estimator + active learning
The error scales cleanly with how *bare* the empty p is (quenched by
σ-hyperconjugation / π-donation — measured in H104/H110/H111).

- **Do:** build a cheap descriptor (empty-orbital occupancy proxy or learned
  fingerprint) that predicts UMA's error *a priori*, so a workflow auto-decides
  UMA-vs-DFT. Combine with #2 into an active-learning loop: UMA proposes →
  estimator flags → DFT labels the flagged → correction refits → envelope grows.

## 4. Scale to sesqui-/diterpenes (where speed wins decisively)
Monoterpenes (C₁₀) are small enough for DFT to keep up; the synergy payoff
explodes for sesqui-(C₁₅)/di-(C₂₀)terpene cascades with combinatorial
conformer/intermediate spaces.

- **Do:** confirm UMA's reliability holds at that scale (same tertiary/allylic
  chemistry) and demonstrate the full explore→refine workflow on a real collaborator-relevant
  diterpene cascade.

## 5. Is the disease UMA-specific or universal to equilibrium-trained MLIPs?

- **Do:** re-run the bare-empty-p benchmark on MACE-OFF / ANI / other MLIPs. If
  they fail the same way, it is a fundamental training-coverage law for MLIPs on
  reactive intermediates — a far bigger claim than a single model's bug. Also
  probe other electron-deficient species (silylium, boranes, main-group cations).

## 6. Open-shell / antiaromatic class
UMA fails on carbenes (halocarbene singlet–triplet sign) and antiaromatic Cp⁺
(misses Jahn–Teller, −200 kcal/mol triplet) — the secondary class.

- **Do:** systematically map UMA's spin-state and antiaromatic/aromaticity
  failures against multireference references; test whether a spin-aware or
  multireference-labeled correction helps. Directly on-target for the aromaticity group's
  reactive-intermediate / aromaticity research.

## Traceability to the reviewer's last-round verdicts (`feedback/verdicts_2026-07-21.json`)

- **Dir 2 (retraining)** ← T10 (the reviewer's explicit "how can it be retrained / what
  molecules to add") + open question "how far into the retraining prescription."
  Tightest match to a stated ask.
- **Dir 6 (open-shell/antiaromatic)** ← T1/T2/T3, all marked `interesting`; kept
  **6th** because the reviewer ranked this class *secondary* to the carbocation axis. Also
  aligns with the aromaticity/hyperconjugation direction.
- **Dir 1 (barriers/TSs)** ← new, but obeys the reviewer's master filter T5 ("relevant to
  carbocation/NP chemistry?"): barriers *are* the mechanism. Re-frames the
  stretched-bond lesson (F₂, which the reviewer demoted off-domain) toward the in-domain
  place stretched bonds matter — rearrangement TSs.
- **Dir 4 (sesqui/diterpenes)** ← T10's "mechanistic natural product chemistry."
- **Dir 3 (error estimator)** ← T7 (the reviewer marked `boring` as *novelty*, but the reviewer's own
  meta-principle: "do the boring correctness work — it licenses the exciting
  claims"). Operationalizes "know when to trust UMA."
- **Dir 5 (cross-MLIP generality)** ← gated by the reviewer's UNANSWERED open question:
  venue = MLIP-methods vs physical-organic. Headline only if MLIP-methods.
  **Needs the reviewer's input before prioritizing.**

Resolved since last round (so next round does not re-litigate): the reviewer's `verify`
items T4 methyl (DFT +104.7 → headline) and B1 α-terpinyl (synergy) are closed;
B2 tert/sec gap is the only `verify` left, now a trivial PySCF run. The reviewer's `chase`
B5 (vinyl, CCSD(T)) and `hold` B3/B6 are settled. The reviewer's `interesting` B4/T6
bridgehead was **demoted** — UMA/xTB/DFT all agree, not a flaw — so it is
deliberately dropped, not carried forward.

## Parked (still want higher theory)
2-norbornyl classical-vs-nonclassical stabilization (CCSD(T)/CBS), and the
tert/sec C₄H₉⁺ gap to sub-kcal (B2) — both cheap to run now that the PySCF stage
(`run_dft.py`) exists.
