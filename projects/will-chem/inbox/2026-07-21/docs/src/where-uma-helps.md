# What research excels with UMA

Grounded in the iteration-2 evidence (`DIARY_ITER2.md`, `UMA_FLAWS.md`,
`RETRAIN_SPEC.md`, `synergy.py`). Framed around two research areas: **terpene/terpenoid carbocation cascade
mechanisms**, and **aromaticity, hyperconjugation, σ-delocalization, and
reactive-intermediate stabilization**.

## The one-line answer
UMA excels wherever the science is **exploration-limited** — too many structures,
conformers, or pathways for DFT to enumerate — **and** the chemistry stays in
UMA's validated regime (closed-shell, delocalized/hyperconjugated cations; no
bare empty p; no open-shell/antiaromatic centers). It is a **fast front-end that
proposes; DFT disposes.** Iteration 2 measured exactly where that regime is.

## Where UMA is a force multiplier

### 1. Terpene cyclization cascade mapping
A monoterpene/diterpene cascade is a *network*: dozens of carbocation
intermediates, each with many conformers, connected by competing
Wagner–Meerwein/hydride-shift/cyclization steps. This is precisely the
combinatorial space DFT cannot afford to enumerate — and precisely where UMA is
both **fast and validated**:
- **Fast:** UMA relaxed 20–34 conformers of a C₁₀H₁₇⁺ terpene cation into
  6–11 basins in **2–4 minutes**; the same DFT sweep is ~0.6–0.8 h (`synergy.py`).
- **Reliable here:** cyclic tertiary/allylic terpene cations match DFT to
  **~1–3 kcal/mol**; Markovnikov tert/sec (H109), ring-expansion (H127),
  Wagner–Meerwein driving forces (neopentyl→tert-amyl, pinacolyl; H128/H129), and
  the C₁₀H₁₇⁺ isomer manifold (H107/H142) all cross-confirm against xTB **and**
  DFT. No bare empty p appears in these intermediates, so the one UMA disease
  never bites.
- **Workflow:** UMA enumerates conformers → basins → candidate rearrangement
  pathways; DFT refines the handful of stationary points and barriers that
  decide selectivity. UMA finds the *rare* global-minimum conformer a single
  relaxation misses (settled B1: the α-terpinyl reference conformer sits ~1.3
  kcal below the naive one). This is the highest-leverage use here.

### 2. Reaction-network / PES pre-screening
Before spending DFT, use UMA to map which minima exist and which shifts/cyclizations
are barrierless vs real (iteration 1 already saw many barrierless WM shifts). UMA
screens the topology; DFT quantifies the survivors.

### 3. Conformer ensembles for large terpenoids / natural products
Dense conformational sampling of floppy substrates where DFT is prohibitive —
UMA ranks, DFT confirms the top few (the `synergy.py` pattern, which also caught
that the H107 linalyl "divergence" was just sampling).

### 4. Substituent / analog screening
Scanning substituent or substrate series for stability trends — the isodesmic
ladders cross-confirmed broadly (H101, H124–H132), so UMA is a trustworthy fast
screen for *relative* trends in ordinary cations.

## Where UMA helps for aromaticity / hyperconjugation work
Closed-shell aromaticity/hyperconjugation/σ-delocalization is a UMA **strength**,
now validated:
- Hyperconjugation geometric fingerprints (β-C–H +18 mÅ, β-Si bridging +30.7,
  cross-confirmed at xTB H121), aromatic stabilization energies (benzene ASE ~37,
  tropylium, cyclopropenyl), resonance delocalization (allyl/benzyl/carboxylate/
  nitro symmetrization), cation–π, H-bonds/RAHB — all reproduced.
- So for **quantifying stabilization of closed-shell reactive intermediates**,
  UMA is a fast, reliable screen — and, notably, sometimes **more accurate than
  xTB**: for the 2-propenyl (α-methyl vinyl) cation, DFT (+8.7) confirms UMA
  (+7.9) and refutes xTB (−17.5) (H171); UMA also tracks the experiment-validated
  cubane strain where xTB underestimates it (H172).

## The guardrails (hand these to DFT / higher theory)
The reactive-intermediate edge cases the aromaticity group cares about are exactly
UMA's failures — so the map matters most there:
- **Bare-empty-p cations** — methyl, non-α-substituted vinyl, primary /
  non-classical small bridged cations, β-halonium open forms. Over-stabilized by
  up to +100 kcal/mol (DFT/CCSD(T)-confirmed). Never let UMA rank these.
- **Open-shell / antiaromatic / degenerate** — carbenes (halocarbene singlet–
  triplet wrong sign), cyclopentadienyl cation (misses antiaromatic Jahn–Teller,
  −200 kcal/mol triplet). Use DFT or multireference.

## Bottom line
In practice: **use UMA as the exploration engine for terpene carbocation
landscapes** and as a **fast screen for closed-shell
aromaticity/hyperconjugation stabilization**, always paired with DFT on the
few structures that matter and with the iteration-2 failure map telling you which
regime you're in. The combination is strictly more capable than DFT alone: UMA
gives near-DFT answers over a search space DFT could never traverse; DFT keeps
UMA honest exactly where it breaks.
