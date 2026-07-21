# will-chem — UMA for terpene & carbocation chemistry

Running **UMA** (Meta FAIR's *Universal Model for Atoms*, a machine-learning
interatomic potential) on the chemistry that **William DeSnoo** (Dean Tantillo's
group, UC Davis) works on: **terpene/terpenoid biosynthesis, carbocation
rearrangements, and reaction mechanisms**.

DeSnoo's published work (incl. *Nature* 2024 on stereoselective carbocation
shifts, and diterpene-cyclization mechanism studies) leans on DFT and molecular
dynamics to rank carbocation intermediates and map rearrangement pathways. UMA
is a fast, general-purpose surrogate for exactly those single-point energies and
geometry relaxations — seconds on CPU instead of hours of DFT.

## What this runs

`uma_desnoo/` builds three sets of DeSnoo-relevant systems, relaxes each with
UMA (via the ASE calculator), and reports energies:

1. **C4H9+ carbocation stability ladder** — does UMA reproduce
   tertiary > secondary > primary stability? That ordering is the thermodynamic
   engine of hydride/methyl shifts and of terpene cascade selectivity.
2. **Monoterpene branch-point** — limonene (neutral C10H16) and the
   **α-terpinyl cation** (C10H17+), the central hub of monoterpene cyclization.
3. **Non-classical carbocation** — the **2-norbornyl cation**, archetype of the
   σ-bridging / Wagner–Meerwein behaviour seen in bornyl/camphene terpene
   rearrangements.

Each system is a SMILES + total charge + spin multiplicity (see
`uma_desnoo/systems.py`). UMA molecular predictions use the `omol` task with
per-structure charge/spin.

## Setup

```bash
pip install torch --index-url https://download.pytorch.org/whl/cpu
pip install --ignore-installed packaging fairchem-core ase rdkit
```

UMA weights are **gated** on Hugging Face. Once, do:

```bash
# 1. Request access (manual approval): https://huggingface.co/facebook/UMA
# 2. Create a read token:               https://huggingface.co/settings/tokens
export HF_TOKEN=hf_xxx        # or: hf auth login
```

## Run

```bash
# Validate the chemistry side without downloading UMA:
python -m uma_desnoo.run_uma --dry-run

# Relax all systems with UMA and write results/uma_results.json:
python -m uma_desnoo.run_uma --model uma-s-1p1 --fmax 0.05 --steps 200

# Render the Markdown report (renders UMA and/or the xTB reference):
python -m uma_desnoo.report
```

Outputs land in `uma_desnoo/results/`: `uma_results.json`, `REPORT.md`, and a
relaxed `*.xyz` per system.

## Charge-aware reference (runs today, ungated)

UMA weights are gated, so as a benchmark this repo also runs **GFN2-xTB**
(`tblite`) — a real, ungated, charge-aware semiempirical method valid for the
carbocations here (neutral-only ML potentials like MACE-OFF/ANI are not). This
is exactly the kind of reference a computational chemist benchmarks an ML
potential against; when UMA runs, `report.py` shows UMA vs xTB side by side.

```bash
pip install tblite
python -m uma_desnoo.run_xtb --fmax 0.05 --steps 300
python -m uma_desnoo.report
```

**GFN2-xTB results (see `uma_desnoo/results/REPORT.md`):**

- **C4H9+ ladder** — *tert*-butyl (tertiary) is most stable; sec-butyl +15.7 and
  isobutyl +16.1 kcal/mol; the primary **n-butyl cation will not hold a
  minimum** (it collapses by hydride shift) — the exact rearrangement chemistry
  DeSnoo studies.
- **Monoterpene** — protonating limonene's ring alkene to the **α-terpinyl
  cation** is downhill by ~43 kcal/mol (bare-proton reference), consistent with
  Markovnikov protonation to a tertiary cation opening the cyclization cascade.
- **2-norbornyl cation** — relaxes to a bridged non-classical minimum.

These are the reference numbers; swapping in a HuggingFace token reruns the
identical pipeline with the actual UMA model for a direct comparison.

## UMA results (real model, `uma-s-1p1`)

With HF access granted for `facebook/UMA`, the real model now runs the same
pipeline (all 7 systems relax and converge in seconds on CPU). Highlights from
`uma_desnoo/results/REPORT.md`:

- **C4H9+ ladder** — UMA reproduces the key result that the **tertiary
  *tert*-butyl cation is most stable** (ΔE = 0). It compresses the rest of the
  ladder into a ~12–13 kcal/mol band (isobutyl 12.4, n-butyl 12.8, sec-butyl
  13.2) — versus xTB's wider spread — and unlike xTB it holds a finite minimum
  for the primary cations rather than collapsing them.
- **Method comparison** — only the C4H9+ ladder is an apples-to-apples
  cross-method comparison (shared formula + charge, so the absolute-energy
  reference cancels). The monoterpene protonation ΔE is *not* directly
  comparable across UMA and xTB — it rides on each method's absolute energy
  zero. `report.py` now states this caveat explicitly.

## Configuration-space exploration (UMA as an engine, not just a ruler)

UMA is fast enough to *map* a potential-energy surface, not just score one
geometry. `uma_desnoo/explore.py` embeds many starting conformers per system,
relaxes each with UMA, and clusters the results into distinct minima — and pools
isomeric starting points into one surface to reveal the rearrangement network.

```bash
python -m uma_desnoo.explore --confs 16
```

Key findings (`uma_desnoo/results/explore_results.json`, narrated in `DIARY.md`):

- **α-terpinyl cation** — the multi-conformer search finds a global minimum
  **2.3 kcal/mol below** the single-ETKDG-guess energy the one-shot pipeline
  reported. Since this cation is the monoterpene branch-point, its reference
  conformer matters; confirming the ordering with DFT is raised in
  `DFT_REQUESTS.md`.
- **C4H9⁺ surface** — pooling all four isomer starts shows only **two real
  basins**: tertiary (only *tert*-butyl feeds it) and secondary/2-butyl (fed by
  isobutyl, n-butyl, *and* sec-butyl). Primary cations are not minima — they
  drain into the secondary basin. The "4-entry ladder" is really 2 basins plus
  rearrangement funnels.

### Calibrating the instrument

Before trusting UMA's map, `uma_desnoo/stress_test.py` checks the invariants a
trustworthy potential must satisfy (**6/6 pass**): determinism, translation and
rotation invariance, charge sensitivity (152 kcal/mol between q=0 and q=+1 on
identical geometry — the `omol` task genuinely uses total charge), basin
stability under 0.15 Å noise, and cross-method agreement with GFN2-xTB on the
isomeric tert–sec gap (Δ = 2.5 kcal/mol, both put tertiary lower).

```bash
python -m uma_desnoo.stress_test
```

## Layout

| File | Purpose |
|---|---|
| `uma_desnoo/systems.py` | The DeSnoo-relevant molecules (SMILES, charge, spin) |
| `uma_desnoo/build_geometry.py` | SMILES → 3D (RDKit ETKDG + MMFF) → ASE Atoms |
| `uma_desnoo/run_uma.py` | Load UMA, relax each system, compute relative energies |
| `uma_desnoo/run_xtb.py` | GFN2-xTB (tblite) charge-aware reference |
| `uma_desnoo/explore.py` | UMA-driven configuration-space search + minima clustering |
| `uma_desnoo/stress_test.py` | Instrument calibration: invariants UMA must satisfy |
| `uma_desnoo/report.py` | Render `REPORT.md` from the JSON results |
| `DIARY.md` | Scientist diary — hypotheses, tests, findings |
| `DFT_REQUESTS.md` | Where UMA/xTB aren't enough and DFT is warranted |
