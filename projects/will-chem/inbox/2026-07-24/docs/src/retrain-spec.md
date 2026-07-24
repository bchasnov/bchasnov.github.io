# Retraining prescription — making an MLIP transferable to carbocation / natural-product mechanism

Constructive output of iteration 2 (see `DIARY_ITER2.md`, `HYPOTHESES_ITER2.md`,
`UMA_FLAWS.md`). Reviewer T10: *"consider how it can be retrained or what
molecules should be added to make it more transferable to carbocation and
mechanistic natural product chemistry."*

## The diagnosis in one sentence
UMA (`uma-s-1p1`, OMol task) has a **single, sharply-localized failure**:
it over-stabilizes an **unquenched, localized empty p orbital** — maximal for the
bare methyl cation (**+104.7 kcal/mol vs B3LYP/def2-TZVP, which itself matches
experiment to 1.6 kcal/mol**), and quenched by *any* real donation into that
orbital (one alkyl σ-hyperconjugation removes 94%; N/O/Cl π-donation removes ~all;
F only partial). Everything else about carbocation physics —
2°/3°/allylic/benzylic/aromatic stabilization, hyperconjugation fingerprints,
Wagner–Meerwein energetics, terpene cyclization — UMA already reproduces to
~1–3 kcal/mol (ordinary 1°/2° rungs match DFT to **<1 kcal/mol**).

## Ab-initio-confirmed reference labels (iteration 2, PySCF)
The augmentation targets below now have real reference numbers, computed here:

| target motif | UMA | reference | UMA error | method |
|---|---|---|---|---|
| methyl cation (bare p) | +22.1 | **−82.6** | **+104.7** | B3LYP/def2-TZVP (=exp) |
| bridged vinyl vs classical | −49.4 | **−3.3** | **−46.1** | CCSD(T)/def2-TZVP |
| β-halonium open vs bridge | +56–67 | **0 to +6** | **+55–60** | B3LYP/def2-TZVP |
| ethyl/isopropyl (control) | ✓ | ✓ | **<1** | B3LYP (UMA already correct) |
| 1- vs 2-norbornyl (bridgehead) | −3.1 | **−2.9** | **~0** | B3LYP (UMA already correct — not a target) |

This means the fix is **narrow and cheap**: the training set does not need "more
carbocations" broadly — it needs the **sparsely-sampled bare-empty-p corner**,
with correct high-level reference energies.

## What to add (molecule classes, in priority order)

Each class below is a set of species UMA currently gets wrong or unverified.
Reference labels should be **CCSD(T)/CBS or DLPNO-CCSD(T)/def2-TZVP** energies
(these are all small); geometries + isodesmic/isomeric framing so the labels are
method-portable.

1. **Bare / minimally-substituted empty-p carbenium ions** *(highest leverage)*
   — CH₃⁺, CH₂F⁺, CHF₂⁺, CF₃⁺, CH₂=CH⁺ (classical vinyl), HC≡C–CH₂⁺ (propargyl),
   and the primary cations that are unstable minima. Reference data: gas-phase
   **hydride affinities** (already tabulated experimentally: CH₃⁺ 312, C₂H₅⁺ 273
   kcal/mol …) plus CCSD(T) for the halomethyls. Evidence: H101 (methyl +104),
   H104/H111 (fluoromethyl/CF₃⁺ residual), H110 (decay).

2. **Non-classical / 3-center-2-electron bridged small cations** *(both minima,
   with correct ordering)* — bridged vs classical vinyl (protonated acetylene:
   true gap ~4–5 kcal/mol, UMA over by ~45–54); H-bridged ethyl; **β-halonium
   ions** (bridged chloronium/bromonium are the minima; UMA wrongly puts an open
   β-halo cation 56–67 kcal/mol below). Reference: CCSD(T)/CBS bridged-vs-open
   pairs. Evidence: H102, H105.

3. **σ-bridged (non-classical) carbocation archetypes** — 2-norbornyl
   (classical-vs-bridged is CCSD(T)-hard), cyclopropylcarbinyl/bicyclobutonium,
   7-norbornenyl. These are the *productive* motifs of terpene Wagner–Meerwein
   chemistry; UMA localizes the bridge geometry correctly but its energetics are
   unvalidated. Reference: CCSD(T) single points on UMA/xTB minima.

4. **Bridgehead cations** — 1-norbornyl, 1-adamantyl, and a small bicyclic
   bridgehead series. Note: UMA and xTB *agree* here (both put 1-norbornyl ~3
   kcal below 2-norbornyl, H103), so this is not confirmed as UMA-specific — but
   because both fast methods may share the limitation, DFT/CCSD(T) reference data
   for bridgehead ions is worth adding to be safe.

5. **(Secondary, off the carbocation axis) open-shell / degenerate species** —
   cyclopentadienyl cation (antiaromatic singlet + triplet), halocarbene
   singlet–triplet gaps (CCl₂, CF₂). UMA's spin-state energetics are
   qualitatively wrong here (iteration 1 FLAW 1/2/7). Lower priority for
   natural-product mechanism, but needed if the model is used on carbenes/
   diradicals. Reference: multireference (CASPT2/MRCI) or experiment.

## What NOT to bother adding
Ordinary 2°/3°/allylic/benzylic/aromatic cations and terpene tertiary/allylic
intermediates — UMA already handles these to chemical accuracy (H101, H107–H109,
H112). Adding more of them wastes label budget on the part that already works.

## How much / validation protocol
- The bare-empty-p corner (classes 1–2) is a few dozen small molecules — a
  **cheap CCSD(T) augmentation set**, not a campaign.
- Validate any retrained model by re-running this repo's iteration-2 ladders
  (`python -m uma_core.mechanisms2 all`): success = the methyl UMA−xTB gap and
  the vinyl/halonium mis-orderings collapse toward the xTB/CCSD(T) values while
  the ordinary-cation and terpene numbers stay put.

## Open items requiring DFT (from `DFT_REQUESTS.md`, still parked)
B1 (α-terpinyl conformer reference — reinforced by H107 linalyl conformer
scatter), B2 (tert/sec gap to chemical accuracy), and CCSD(T) magnitudes for
classes 2–4 above. Iteration 2 has firmed each up with multi-start + xTB +
literature so the DFT, when available, is a single well-scoped set of runs.
