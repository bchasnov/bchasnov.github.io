# Round 3 — driven by the reviewer's round-2 verdicts

Source: `feedback/verdicts_2026-07-22.json`. Round 2 (H101–H200) is
reviewer-validated; round 3 turns the reviewer's "chase" signals into a concrete program.

## Decoded verdicts

- **Receipts accepted.** No pushback on methyl / vinyl / bridgehead (R1–R3);
  terpene questions "good enough, verified with DFT" (R4, hold); F₂ "boring, move
  on" (R5). The iteration-2 conclusions stand.
- **The live thread is UMA-as-a-tool, benchmarked hard (R6, interesting; A5).**
  Not "does it have flaws" but: **is UMA significantly better than xTB for
  conformer search** — does a wide UMA conformer window actually surface the
  *relevant* (low, reactive) conformers, at what **efficiency**, and **for which
  systems**? The reviewer wants a **rule set** keyed on size / bonding / atom types. Venue:
  **both** — document the failures, but *prove* usability via **standard
  conformational-search protocols**, characterize the **energy range of minima**,
  and attempt **constrained TS searches**.
- **All three method directions: chase, each hardened.**
  - **A1 dynamics** → validate against **established / experimental / the experimental collaborator
    branch ratios**, not in a vacuum.
  - **A2 barriers** → include **unusual TSs — dyotropic rearrangements**.
  - **A3 retraining** → **justify** the Δ-learn set; **test transfer** (does
    training on simple unsubstituted carbocations actually improve *applicable*
    systems?); and **add non-planar carbocations** — the reviewer flags these as
    mechanistically central.
- **Benchmark standard (A4):** real, documented systems — **taxadiene** and
  the experimental collaborator papers — compared at **their** level of theory, **mPW1PW91/6-31G(d)**.

## Workstreams
### W1 — Conformer-search rule set (answers R6 + A5) — start here
Systematic UMA-vs-xTB (vs DFT-refined) conformer search across systems spanning
size (C₅ → C₂₀), bonding (saturated / allylic / non-classical), and heteroatoms.
Per system measure: (a) does the true low-energy / reactive conformer get found,
(b) how wide a window is needed, (c) wall-clock efficiency, (d) UMA-vs-xTB minimum
quality after DFT single-points. Deliverable: a **rule set** — where UMA is
strongly preferred, keyed on size/bonding/atoms — and the energy range of minima.

### W2 — Non-planar carbocations (answers A3's specific ask)
Do geometrically constrained / pyramidalized carbocations show the disease? Scan
the pyramidalization penalty (planar → forced non-planar) and a bridgehead-strain
series (adamantyl / bicyclics), UMA vs DFT. These are the mechanistically relevant
"can't planarize" ions; establish whether UMA gets their penalty right (the
bridgehead at mild pyramidalization did — where does it break?).

### W3 — Scale-up on documented systems (A4)
Taxadiene / the experimental collaborator cascade cations at C₂₀; UMA conformer search + energetics
compared to mPW1PW91/6-31G(d) reference numbers from their papers.

### W4 — Barrier / TS benchmark incl. dyotropic (A2)
UMA vs DFT for 1,2-H / 1,2-Me shift and cyclization barriers via NEB, plus a
dyotropic rearrangement TS; attempt constrained TS searches (A5).

### W5 — Rational Δ-learning set + transfer test (A3)
Justify the augmentation set (bare-empty-p + **non-planar** carbocations); fit the
correction; test whether it improves *applicable* substituted/mechanistic systems,
not just the training toys.

## Priority
W1 and W2 are tractable now with existing tooling (`synergy.py`, `iter2.py`,
`run_dft.py`) and answer the reviewer's richest comments — start there. W3/W4/W5 follow,
benchmarked at mPW1PW91/6-31G(d) against documented systems.
