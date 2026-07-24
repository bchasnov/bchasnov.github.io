# UMA report

Carbocation & terpene chemistry relevant to an experimental collaborator.

- **Model:** `uma-s-1p1` (task `omol`)  
- **Device:** cpu  
- **Force convergence:** fmax = 0.05 eV/Å  
- **Systems:** 7

Energies are potential energies in eV (ASE convention). Relative energies within a group are in kcal/mol, referenced to the lowest-energy member (see each group's note for interpretation).


## Part 1 — C4H9+ carbocation stability ladder

All four are C4H9+ isomers, so their relative energies are a clean stability ranking. Expected ordering: tertiary > secondary > primary. This ordering is the thermodynamic driving force behind hydride/methyl shifts and is why terpene cyclization cascades funnel through tertiary/allylic cations.

| System | Formula | Charge | E_final (eV) | ΔE (kcal/mol) | fmax | Converged |
|---|---|---|---|---|---|---|
| tert-butyl cation (tertiary) | C4H9 | +1 | -4285.7755 | 0.00 | 0.024 | yes |
| isobutyl cation (primary) | C4H9 | +1 | -4285.2361 | 12.44 | 0.033 | yes |
| n-butyl cation (primary) | C4H9 | +1 | -4285.2199 | 12.81 | 0.047 | yes |
| sec-butyl cation (secondary) | C4H9 | +1 | -4285.2023 | 13.22 | 0.044 | yes |

**Most stable C4H9+ isomer:** tert-butyl cation (tertiary) (reference, ΔE = 0). Textbook expectation: the tertiary *tert*-butyl cation. Note the primary n-butyl cation typically will not hold a minimum — it collapses via hydride shift, exactly the rearrangement chemistry of interest.

## Part 2 — Monoterpene cyclization branch-point

Limonene (neutral C10H16) and the alpha-terpinyl cation (C10H17+) it maps to on protonation. Because the cation = limonene + H+, the ΔE here is the **protonation energy** (bare-proton reference): how favourable it is to protonate the ring alkene to the tertiary cation that opens the monoterpene cyclization cascade. It is NOT an isomer-stability comparison.

| System | Formula | Charge | E_final (eV) | ΔE (kcal/mol) | fmax | Converged |
|---|---|---|---|---|---|---|
| alpha-terpinyl cation (C10H17+) | C10H17 | +1 | -10637.7814 | 0.00 | 0.041 | yes |
| (R)-limonene (neutral monoterpene) | C10H16 | +0 | -10629.1060 | 200.06 | 0.041 | yes |

## Part 3 — Non-classical (sigma-bridged) carbocation

The 2-norbornyl cation, archetype of sigma-participation / Wagner-Meerwein behaviour that pervades bornyl/camphene terpene rearrangements. Reported as a single relaxed structure/energy.

| System | Formula | Charge | E_final (eV) | ΔE (kcal/mol) | fmax | Converged |
|---|---|---|---|---|---|---|
| 2-norbornyl cation (non-classical archetype) | C7H11 | +1 | -7428.4932 | 0.00 | 0.046 | yes |

---

_Structures relaxed from RDKit ETKDG guesses. Relaxed geometries are in `results/*.xyz`._




# GFN2-xTB report

Carbocation & terpene chemistry relevant to an experimental collaborator.

- **Model:** `GFN2-xTB (tblite)` (task `reference`)  
- **Device:** cpu  
- **Force convergence:** fmax = 0.05 eV/Å  
- **Systems:** 7

Energies are potential energies in eV (ASE convention). Relative energies within a group are in kcal/mol, referenced to the lowest-energy member (see each group's note for interpretation).


## Part 1 — C4H9+ carbocation stability ladder

All four are C4H9+ isomers, so their relative energies are a clean stability ranking. Expected ordering: tertiary > secondary > primary. This ordering is the thermodynamic driving force behind hydride/methyl shifts and is why terpene cyclization cascades funnel through tertiary/allylic cations.

| System | Formula | Charge | E_final (eV) | ΔE (kcal/mol) | fmax | Converged |
|---|---|---|---|---|---|---|
| tert-butyl cation (tertiary) | C4H9 | +1 | -345.0411 | 0.00 | 0.045 | yes |
| sec-butyl cation (secondary) | C4H9 | +1 | -344.3609 | 15.69 | 0.041 | yes |
| isobutyl cation (primary) | C4H9 | +1 | -344.3410 | 16.14 | 0.050 | yes |
| n-butyl cation (primary) | C4H9 | +1 | -343.3888 | 38.10 | 0.358 | no |

**Most stable C4H9+ isomer:** tert-butyl cation (tertiary) (reference, ΔE = 0). Textbook expectation: the tertiary *tert*-butyl cation. Note the primary n-butyl cation typically will not hold a minimum — it collapses via hydride shift, exactly the rearrangement chemistry of interest.

## Part 2 — Monoterpene cyclization branch-point

Limonene (neutral C10H16) and the alpha-terpinyl cation (C10H17+) it maps to on protonation. Because the cation = limonene + H+, the ΔE here is the **protonation energy** (bare-proton reference): how favourable it is to protonate the ring alkene to the tertiary cation that opens the monoterpene cyclization cascade. It is NOT an isomer-stability comparison.

| System | Formula | Charge | E_final (eV) | ΔE (kcal/mol) | fmax | Converged |
|---|---|---|---|---|---|---|
| alpha-terpinyl cation (C10H17+) | C10H17 | +1 | -805.7911 | 0.00 | 0.044 | yes |
| (R)-limonene (neutral monoterpene) | C10H16 | +0 | -803.9326 | 42.86 | 0.042 | yes |

## Part 3 — Non-classical (sigma-bridged) carbocation

The 2-norbornyl cation, archetype of sigma-participation / Wagner-Meerwein behaviour that pervades bornyl/camphene terpene rearrangements. Reported as a single relaxed structure/energy.

| System | Formula | Charge | E_final (eV) | ΔE (kcal/mol) | fmax | Converged |
|---|---|---|---|---|---|---|
| 2-norbornyl cation (non-classical archetype) | C7H11 | +1 | -547.0953 | 0.00 | 0.036 | yes |

---

_Structures relaxed from RDKit ETKDG guesses. Relaxed geometries are in `results/*.xyz`._





## Method comparison — relative energies (kcal/mol)

**Read this carefully.** Only **Part 1** is a valid cross-method comparison: all four C4H9+ species share formula and charge, so each method's absolute-energy reference cancels and the ΔE ladder is apples-to-apples. **Part 2 is not directly comparable across methods** — its ΔE is a bare-proton protonation energy between species of different formula *and* charge, so it depends on each method's absolute energy zero (this is why UMA and xTB differ by ~160 kcal/mol here; it is a reference-scale artifact, not a chemistry disagreement). **Part 3** has a single member, so its column is trivially 0.00. To compare UMA and xTB on protonation, use a consistent proton reference within each method rather than the numbers below.


### Part 1 — C4H9+ carbocation stability ladder

| System | UMA | GFN2-xTB |
|---|---|---|
| tert-butyl cation (tertiary) | 0.00 | 0.00 |
| sec-butyl cation (secondary) | 13.22 | 15.69 |
| isobutyl cation (primary) | 12.44 | 16.14 |
| n-butyl cation (primary) | 12.81 | 38.10 |

### Part 2 — Monoterpene cyclization branch-point

| System | UMA | GFN2-xTB |
|---|---|---|
| (R)-limonene (neutral monoterpene) | 200.06 | 42.86 |
| alpha-terpinyl cation (C10H17+) | 0.00 | 0.00 |

### Part 3 — Non-classical (sigma-bridged) carbocation

| System | UMA | GFN2-xTB |
|---|---|---|
| 2-norbornyl cation (non-classical archetype) | 0.00 | 0.00 |