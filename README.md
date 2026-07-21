# CiPA Solver Fidelity & Hardware Portability — Manuscript Draft

## Contents
- `main.tex` — Introduction (Sec. I) and Related Work (Sec. II), IEEEtran journal class. Also includes title/author block, abstract (placeholder numbers), keywords, and a References section, so the file compiles standalone.

## Compiling
Requires `IEEEtran.cls`, standard on Overleaf and any full TeX Live / MiKTeX install. Not bundled in this sandbox (no network/root access here to fetch it), so compilation was verified locally using an article-class stand-in with stubbed IEEEtran macros — output was clean (7 pages, no undefined references). On a normal LaTeX setup, just run `pdflatex main.tex` (twice, for reference numbering).

## Citations used
Only the sources named in the handoff document (Section 8 + "external literature still to be gathered"), nine total, numbered in strict order of first appearance:
1. Li et al. 2017 — CiPA/qNet (*Circ Arrhythm Electrophysiol*)
2. O'Hara et al. 2011 — ORd model (*PLoS Comput Biol*)
3. Hindmarsh et al. 2005 — SUNDIALS/CVODE (*ACM TOMS*)
4. Pramawijaya, Wibowo, Usman 2022 — ICoICT (low-cost computing framing)
5. Heikhmakhtiar et al. 2026 — *Toxicological Research*
6. Kamanditya et al. 2024 — KSME
7. Aroli et al. 2024 — KSME (CardioSim)
8. Fuadah et al. 2024 — KSME
9. Qauli et al. 2025 — KSME

No outside literature was introduced, per instruction.

## Open items before submission (flagged inline as `% TODO` in main.tex)
- Co-author given-name initials for references 4–9 — handoff supplied surnames only.
- Final author list and order (open item #7 in the handoff).
- Abstract's headline numbers and practical recommendation — marked `[TBD]`, to be filled once Iga's experiments conclude.
- Acknowledgements/funding — marked `[TBD]`.

## Not yet drafted
Methods, Results, Discussion, Conclusion — these depend on the experimental data described in the handoff (Phases 1–3) and should be drafted once results land.
