# Project Handoff — CiPA In-Silico Cardiotoxicity: Solver Fidelity & Hardware Portability Study

**Prepared for:** AI agent / collaborator continuing manuscript drafting
**Principal investigator:** Iga Narendra Pramawijaya (Telkom University / Universitas Telkom, Bandung)
**Status:** Scope locked. Experiments not yet run. External literature search complete (2 Aug 2026) — see Section 8.1. Manuscript drafting in progress (Introduction, Related Work, Methodology written; Results/Discussion pending data).
**Handoff date:** 21 July 2026 (updated 3 Aug 2026 with literature search results)

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

Core framework citations already secured: O'Hara et al. 2011 (ORd model), Li et al. 2017 on CiPA/qNet, SUNDIALS/CVODE (Hindmarsh et al. 2005).

---

## 8.1 External literature (gathered, ready to cite)

**Literature search completed 2 Aug 2026 — result: the gap is confirmed open.** No existing paper benchmarks CiPA/ORd torsadogenic risk classification across solver choice *and* ARM/low-cost hardware together. This protects the novelty claim. The closest work benchmarks solvers or hardware separately, and only on non-ARM HPC/GPU targets. All nine load-bearing/near-load-bearing sources below have already been integrated into `main.tex` (Introduction, Related Work, Methods, References); the rest are optional depth, not yet cited.

Organized by the role each cluster plays in the manuscript. DOIs/URLs included so the drafting agent can pull full bibliographic detail (some author lists were not fully visible in search snippets — verify before final submission; every new `\bibitem` in `main.tex` is flagged with a `% TODO` comment for this).

**A. CiPA framework and qNet foundation** (supplements Section 8's author-list; used in Introduction/Related Work — already covered by existing li2017/ohara2011 citations, not separately added)
1. Li, Z., et al., "Improving the In Silico Assessment of Proarrhythmia Risk by Combining hERG (Human Ether-à-go-go-Related Gene) Channel–Drug Binding Kinetics and Multichannel Pharmacology," *Circulation: Arrhythmia and Electrophysiology*, 2019 — likely the same paper already cited as `li2017` (Feb 2017, e004628); year discrepancy in search snippet, not re-added.
2. "Introduction to in silico model for proarrhythmic risk assessment under the CiPA initiative," *Translational and Clinical Pharmacology*, 27(1), 2019. doi:10.12793/tcp.2019.27.1.12 — optional depth, not yet cited.
3. Yang, P.C., et al., "A computational pipeline to predict cardiotoxicity: From the atom to the rhythm," *Circulation Research*, 2020 — reserved for Discussion (not yet drafted); not yet cited.

**B. Solver/numerical-method sensitivity in biological/ODE simulation** (Introduction + Methods — cited as `benchmarking2021` and `eulerprecision2013`)
4. "Benchmarking of numerical integration methods for ODE models of biological systems," *Scientific Reports*, 11, 2021. doi:10.1038/s41598-021-82196-2 — **strongest single citation for this paper.** Cited in Introduction (framing the reproducibility gap) and Related Work Sec. II-C.
5. "The most precise computations using Euler's method in standard floating-point arithmetic applied to modelling of biological systems," *Computer Methods and Programs in Biomedicine*, 2013. doi:10.1016/j.cmpb.2013.03.014 — cited in Methods Phase 1, justifying fine-step explicit Euler as ground truth.
6. Krishnapriyan et al. / stiff-ODE convergence literature — optional, cite only if reviewers push back on why CVODE is needed alongside Euler. Not yet cited.
7. Rackauckas et al. (SciML notes on stiffness) — optional background citation for a formal stiffness definition in Methods, if the venue expects one. Not yet cited.

**C. Hardware acceleration precedent in cardiac electrophysiology** (Related Work Sec. II-D — cited as `bartocci2015`, `sachetto2025`, `marinsdelima2025`, `torchcor2025`)
8. Sachetto Oliveira, R., et al., "Toward cardiac electrophysiology digital twins with an efficient open source scalable solver on GPU clusters," *Scientific Reports*, 2025. doi:10.1038/s41598-025-33709-w — MonoAlg3D; ~6.6× GPU speedup, uses O'Hara–Rudy family model directly; structural template for Table 3/4.
9. Marins de Lima, L., Sachetto Oliveira, R., Campos, F., Arantes Berg, L., Oliveira Campos, J. de, Weber dos Santos, R., "Benchmarking Open Cardiac Electrophysiology Simulators: MonoAlg3D and OpenCARP," *2025 Computing in Cardiology (CinC)*. doi:10.22489/cinc.2025.376 — direct methodological template; mirror their normalized-runtime approach in Table 4.
10. Bartocci, E., et al. (or equivalent), "GPU accelerated solver for nonlinear reaction–diffusion systems. Application to the electrophysiology problem," 2015. doi:10.1016/j.cpc.2015.07.024 — ~164× ODE-solver acceleration; foundational "hardware matters" citation.
11. "Toward GPGPU accelerated human electromechanical cardiac simulations," *PMC*, 2014. PMC4016759 — same 164× figure, human left-ventricle scale; optional companion to #10, not yet cited.
12. "An Adaptive Step Size GPU ODE Solver for Simulating the Electric Cardiac Activity," ResearchGate 228443905 — optional, not yet cited.
13. Bartocci, E., "Simulating Cardiac Electrophysiology in the Era of GPU-Cluster Computing," ResearchGate 259480066 — optional, not yet cited.
14. "TorchCor: High-Performance Cardiac Electrophysiology Simulations with the Finite Element Method on GPUs," arXiv:2510.12011, 2025 — cited in Introduction (accessibility argument, paraphrased not quoted).

**D. Verification/validation methodology for regulatory-adjacent in-silico models** (Methods — cited as `invitro2020`)
15. "In silico trials: Verification, validation and uncertainty quantification of predictive models used in the regulatory evaluation of biomedical products," *Progress in Biophysics and Molecular Biology*, 2020. doi:10.1016/j.pbiomolbio.2019.09.001 — frames Phase 1/2 as calculation verification, not just benchmarking.

**E. ARM / low-cost hardware for scientific and HPC workloads** (Introduction/Related Work — cited as `armhpc2023`, `awsgraviton`)
16. "Are we ready for broader adoption of ARM in the HPC?," NSF PAR, 10404023 — direct academic framing of the ARM angle.
17. AWS, "AWS Graviton Processors for HPC workloads" — supporting/informal citation for price-performance claims; verify current figures at time of writing.

> **Citation count:** `main.tex` now carries 22 references total (13 original + 9 newly integrated from this list: items 4, 5, 8, 9, 10, 14, 15, 16, 17). Items 1–3, 6, 7, 11–13 remain optional depth and are not yet in the bibliography.

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