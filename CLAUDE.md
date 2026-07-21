# Project Handoff — CiPA In-Silico Cardiotoxicity: Solver Fidelity & Hardware Portability Study

**Prepared for:** AI agent / collaborator continuing manuscript drafting
**Principal investigator:** Iga Narendra Pramawijaya (Telkom University / Universitas Telkom, Bandung)
**Status:** Scope locked. Experiments not yet run. Manuscript not yet started.
**Handoff date:** 21 July 2026

---

## 1. What this document is

Iga is running the computational work (code, simulations, benchmarking). The receiving agent is responsible for **manuscript production** — Abstract, Introduction, Related Work, Methods narrative, Discussion, Conclusion, figure/table captions — while Iga supplies raw results.

This document contains everything needed to begin drafting the non-results sections **before** the data arrives. Draft against the structure below; slot in numbers when they land.

---

## 2. Project in one sentence

Quantify the **speed–accuracy–cost trade-off** of ODE solver choice and time-step size in CiPA-framework in-silico cardiotoxicity simulation, and establish whether commodity and low-cost ARM hardware can run credible torsadogenic risk screening.

---

## 3. Why this is worth publishing

Three framings, usable individually or combined:

1. **Reproducibility.** CiPA-based in-silico assay results are reported in the literature without systematic accounting for solver and time-step effects. If risk classification shifts with numerical configuration, that is a reproducibility concern the field should know about.
2. **Accessibility / low-cost computing.** If a MacBook-class ARM machine or a cheap ARM cloud instance reproduces reference outputs within acceptable error, regulatory-adjacent cardiotoxicity screening becomes viable for resource-constrained labs — directly relevant to Indonesian and broader Global South research contexts.
3. **Practical guidance.** Deliver a defensible recommendation: *"for this class of simulation, use solver X at time step Y on platform Z."* Concrete, citable, useful.

The novelty is modest and deliberately so. This is a solid methods/benchmarking contribution, not a breakthrough claim. Do not oversell it in the Abstract.

---

## 4. Methodology (locked)

### Phase 1 — Ground truth
- **Method:** Explicit **Euler** integration at a very fine time step (interval subdivided into very small increments, stepped sequentially).
- **Rationale:** Slow but transparent and trustworthy. No adaptive-step black box between the model and the reference output.
- **Scope:** Restricted to the **stratified drug subset** (Section 5), *not* the full CiPA panel. Full-panel fine-step ground truth was explicitly ruled out — runtime would extend to days and blow the timeline.
- **Output:** Reference biomarker traces and derived values per drug, treated as gold standard for all subsequent error computation.

### Phase 2 — Solver comparison
Same drug subset, re-run under faster configurations:
- **CVODE** (SUNDIALS family; adaptive, event-capable — the team's preferred production solver)
- **Euler at coarser time steps** (a sweep of increasing Δt)

Measure divergence from Phase 1 reference. The key question: **at what Δt does the risk classification flip?** Not just "does the waveform drift" — does the *decision* change. That is the finding with teeth.

### Phase 3 — Hardware portability
Deploy the validated configuration across four compute targets:

| # | Platform class | Example | Note |
|---|---|---|---|
| 1 | x86 CPU, consumer | gaming/workstation laptop | baseline reference |
| 2 | x86 + discrete GPU | same class, GPU-enabled | GPU path exists in prior work |
| 3 | ARM local (Apple Silicon) | MacBook Pro (M-series) | strong perf-per-watt |
| 4 | ARM cloud | Graviton-class or equivalent | the cost-efficiency argument |

Report per platform: wall-clock time, peak memory, numerical result agreement, and (for cloud) **cost per completed drug simulation**. The cost figure is what elevates this from a benchmark to an argument.

### Phase 4 — Optional, time permitting
Expand the drug panel beyond the stratified subset.

---

## 5. Drug selection

**Approach:** stratified sample, **6–8 drugs**, spread across CiPA torsadogenic risk categories (high / intermediate / low). Full-panel coverage was considered and rejected — the paper's claim is about *representative* solver and hardware behaviour, not exhaustive screening.

> **OPEN ITEM — ask Iga:** the final drug list and which risk tier each occupies. Leave a placeholder table in the draft.

---

## 6. Logging schema (agreed)

Instrumentation is to be built into the simulation loop **before** runs begin, not reconstructed afterward. One structured record (CSV or JSON) per run:

```
run_id
drug_id
drug_risk_tier          # high / intermediate / low
solver                  # euler_fine | euler_coarse | cvode
time_step               # Δt
platform                # x86_cpu | x86_gpu | arm_local | arm_cloud
platform_spec           # CPU model, core count, RAM, GPU model, OS
wall_clock_seconds
peak_memory_mb
biomarker_values        # APD90, qNet, dVm/dt_max, CaT amplitude, triangulation, ...
error_vs_ground_truth   # per biomarker: absolute + relative
risk_classification     # predicted tier under this configuration
classification_matches_ground_truth   # boolean
```

The last two fields are the paper's headline. Everything else is supporting evidence.

---

## 7. Model & software context

- **Cell model:** O'Hara–Rudy (ORd) human ventricular model — Iga's established platform across prior publications.
- **Framework:** CiPA (Comprehensive in vitro Proarrhythmia Assay) initiative; qNet as the principal torsadogenic risk metric.
- **Existing asset:** a GPU implementation of **CardioSim** already exists, is deployed, and is in active use by another group. A separate front end is maintained by others and is **out of scope** here.
- **Deferred:** a standalone CardioSim software/deployment paper. Backlogged, not abandoned.

---

## 8. Author's prior work (cite these; they establish the research programme)

- Heikhmakhtiar, Qauli, Fu'adah, Pramudito, **Pramawijaya**, et al. "Drug induced TdP risks classification assay using electro-mechanical models of human ventricle based on CiPA framework." *Toxicological Research* 42(2), 217–233, 2026.
- Kamanditya, Qauli, Marcellinus, **Pramawijaya**, Lim. "Optimized qNet for Torsadogenic Drug Risk Classification via In Silico Simulation and Ordinal Logistic Regression." KSME Spring/Autumn Conference, 113, 2024.
- Aroli, Qauli, **Pramawijaya**, Hanum, Lim. "CardioSim: A Graphical User Interface-Based Simulator for Evaluating Drug Toxicity in Cardiac Pharmacology." KSME, 106, 2024.
- Fuadah, Pramudito, **Pramawijaya**, Marcellinus, Lim. "A Pilot Study of In-Silico Human Electro-Mechanical Ventricular Model for Drug Induced TdP Risk Assessment with Machine Learning." KSME, 95, 2024.
- Qauli, **Pramawijaya**, Lim. "The effect of MPOXV candidate drugs (Ribavirin and Mitoxantrone) on Cardiac Electrophysiology: an in silico assay using the ORd ventricular cell model." KSME, 101, 2025.
- **Pramawijaya**, Wibowo, Usman. "Parameter Investigation in Low Computing Cost Model-Based EfficientDet for UAV Object Detection." ICoICT 2022. *(Supports the low-cost-computing framing in the Introduction.)*

External literature still to be gathered: O'Hara et al. 2011 (ORd model), Li et al. on CiPA/qNet, SUNDIALS/CVODE (Hindmarsh et al. 2005), plus any prior work on numerical sensitivity in cardiac electrophysiology simulation. **Search for the latter specifically** — if someone has already done this, the framing must shift toward the hardware/cost angle.

---

## 9. Proposed manuscript structure

1. **Abstract** — problem, approach, headline numbers, practical recommendation.
2. **Introduction** — CiPA and the shift to in-silico proarrhythmia assessment; the unexamined role of numerical configuration; the accessibility argument for low-cost hardware; contributions listed explicitly (3–4 bullets).
3. **Related Work** — CiPA in-silico assays; ORd model applications; ODE solvers in cardiac electrophysiology; hardware acceleration and portability in biomedical simulation.
4. **Methods** — ORd model and CiPA protocol; biomarker definitions; drug selection and stratification rationale; ground-truth definition; solver configurations; hardware platforms; metrics and logging schema.
5. **Results** — ground-truth reference traces; solver accuracy vs Δt; runtime scaling; cross-platform timing/memory; cost per simulation (cloud); classification agreement matrix.
6. **Discussion** — where accuracy breaks down; whether classification is robust to numerical choices; feasibility on commodity/ARM hardware; implications for resource-constrained labs; limitations (subset not full panel, single cell model, one framework).
7. **Conclusion** — a concrete configuration recommendation.
8. Data/code availability, acknowledgements, references.

### Figures & tables to plan for
- **T1** Drug subset with risk tiers
- **T2** Hardware platform specifications
- **T3** Solver × Δt → error and runtime
- **T4** Cross-platform runtime, memory, cost-per-run
- **F1** Pipeline schematic (ground truth → solver comparison → hardware benchmark)
- **F2** Representative action potential traces, ground truth vs coarse solvers
- **F3** Error vs Δt (log–log) — expect a visible accuracy cliff
- **F4** Runtime by platform (grouped bars)
- **F5** Classification agreement heatmap: configuration × drug

---

## 10. Target venues

**Primary (realistic, quick turnaround):**
- IJECE — International Journal of Electrical and Computer Engineering (IAES)
- Bulletin of Electrical Engineering and Informatics (IAES)
- JOIV — International Journal on Informatics Visualization
- Kinetik / JITEKI (Indonesian, SINTA-indexed, fast)

**Stretch, if results are strong:**
- IEEE Access
- Journal of Electrocardiology
- Frontiers in Physiology — Cardiac Electrophysiology
- Computer Methods and Programs in Biomedicine

Confirm APC and current turnaround before committing — these change.

---

## 11. Timeline

Target: **1–2 weeks** for computational work, manuscript drafting to run in parallel and conclude shortly after.

- Days 1–3: instrumentation + fine-step Euler ground truth on the drug subset
- Days 4–7: solver comparison sweep
- Days 8–12: hardware deployment and benchmarking
- Days 10–14: results consolidation, figures
- Parallel: agent drafts Abstract, Introduction, Related Work, Methods narrative against this document

---

## 12. Open items to resolve with Iga

1. Final drug list and risk-tier assignment
2. Ground-truth Δt value, and the Δt sweep range for Phase 2
3. CVODE binding in use (Assimulo, scikit-sundials, direct C, other)
4. Exact hardware specifications for all four platforms
5. Whether the GPU path is in scope as a fifth platform or excluded
6. Language and target venue confirmation before Abstract is finalised
7. Author list and order

---

## 13. Working notes for the drafting agent

- Iga prefers **packaged, immediately usable deliverables** over iterative back-and-forth. Produce complete drafts, not fragments requiring assembly.
- Manuscript content in **English**; task discussion may be in Bahasa Indonesia.
- Iga has flagged concern in past projects about prose reading as AI-generated. Vary sentence length, avoid formulaic transitions ("Moreover," "Furthermore," "In addition" stacked in sequence), and cut hedging boilerplate.
- References in **IEEE style, numbered in strict order of first appearance** unless the target venue specifies otherwise.
- Do not fabricate numerical results. Every quantitative claim must trace to Iga's logged data. Use clearly marked placeholders (`[TBD: mean error at Δt=X]`) until data arrives.