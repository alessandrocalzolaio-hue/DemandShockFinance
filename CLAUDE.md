# CLAUDE.MD — Demand Shocks in the Italian Government Bond Markets

**Project:** Demand Shocks in the Italian Government Bond Markets (Paper 1, PhD Thesis)
**Institution:** University of Trento
**Branch:** main

---

## Core Principles

- **Plan first** — enter plan mode before non-trivial tasks; save plans to `quality_reports/plans/`
- **Verify after** — compile/render and confirm output at the end of every task
- **Python is primary** — `paper1.ipynb` is the analysis engine; LaTeX manuscript derives from it
- **Single source of truth** — Beamer `.tex` is authoritative for slides; Quarto `.qmd` derives from it
- **Quality gates** — nothing ships below 80/100
- **[LEARN] tags** — when corrected, save `[LEARN:category] wrong → right` to [MEMORY.md](MEMORY.md)

Cross-session context lives in [MEMORY.md](MEMORY.md); past plans, specs, and session logs are in [quality_reports/](quality_reports/).

---

## Folder Structure

```
DemandShockFinance/
├── CLAUDE.md                        # This file
├── .claude/                         # Rules, skills, agents, hooks
├── Bibliography_base.bib            # Centralized bibliography
├── Figures/                         # Figures and images
├── Preambles/header.tex             # LaTeX headers
├── Slides/                          # Beamer .tex conference slides
├── Quarto/                          # RevealJS .qmd slides + theme
├── paper/                           # LaTeX manuscript (Paper1.tex)
├── data/                            # Raw + processed data (BTC, COMOVEMENT, ticks)
├── docs/                            # GitHub Pages (auto-generated)
├── scripts/
│   ├── main notebook/Paper1.ipynb  # PRIMARY ANALYSIS — Python, 54 cells
│   └── R/                          # Secondary R scripts (if needed)
├── quality_reports/                 # Plans, session logs, merge reports, decision records
├── explorations/                    # Research sandbox (see rules)
├── templates/                       # Session log, quality report templates
└── master_supporting_docs/          # Reference papers (Lengyel, Altavilla et al.)
```

---

## Commands

```bash
# Run main analysis (Python)
cd "scripts/main notebook" && jupyter nbconvert --to notebook --execute Paper1.ipynb

# LaTeX manuscript (3-pass, XeLaTeX)
cd paper && TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode Paper1.tex
BIBINPUTS=..:$BIBINPUTS bibtex Paper1
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode Paper1.tex
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode Paper1.tex

# LaTeX slides (3-pass, XeLaTeX)
cd Slides && TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode Paper1_slides.tex
BIBINPUTS=..:$BIBINPUTS bibtex Paper1_slides
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode Paper1_slides.tex
TEXINPUTS=../Preambles:$TEXINPUTS xelatex -interaction=nonstopmode Paper1_slides.tex

# Deploy Quarto to GitHub Pages
./scripts/sync_to_docs.sh Paper1_slides

# Quality score
python scripts/quality_score.py Quarto/file.qmd

# Palette sync (LaTeX ↔ SCSS)
./scripts/check-palette-sync.sh

# Surface-count sync
./scripts/check-surface-sync.sh
```

---

## Quality Thresholds (advisory)

| Score | Checkpoint | Meaning |
|-------|------|---------|
| 80 | Commit | Good enough to save |
| 90 | PR | Ready for deployment |
| 95 | Excellence | Aspirational |

Enforced by `/commit` (halts + asks for override) **and** — once you run `./scripts/install-hooks.sh` — by a real git pre-commit hook (`.githooks/pre-commit`) that runs the surface-sync + quality (≥80) gates on every commit. Bypass sparingly with `SKIP_QUALITY_GATE=1` or `--no-verify`.

---

## Skills Quick Reference

The full table of all skills lives in [README.md](README.md#skills-claudeskills). Most-used for this project:

- **Paper writing:** `/review-paper` (`--peer`) `/seven-pass-review` `/respond-to-referees` `/verify-claims` `/proofread` `/humanize` `/submission-disclosures`
- **Data / reproducibility:** `/data-analysis` `/audit-reproducibility` `/diagnose` `/replication-package` `/capture-environment`
- **Slides:** `/compile-latex` `/deploy` `/qa-quarto` `/slide-excellence`
- **Research:** `/interview-me` `/lit-review` `/research-ideation`
- **Meta / workflow:** `/commit` `/context-status` `/deep-audit` `/coauthor-brief`

---

## Beamer Custom Environments

| Environment | Effect | Use Case |
| --- | --- | --- |
| `keybox` | Gold background box | Key empirical results |
| `definitionbox[Title]` | Blue-bordered titled box | Formal econometric definitions |

## Quarto CSS Classes

| Class | Effect | Use Case |
| --- | --- | --- |
| `.smaller` | 85% font | Dense regression tables |
| `.positive` | Green bold | Statistically significant coefficients |

---

## Paper Progress Tracker

| Artifact | File | Status | Notes |
| --- | --- | --- | --- |
| Main analysis | `scripts/main notebook/Paper1.ipynb` | Active | Python, 54 cells, 10+ robustness checks |
| Manuscript | `paper/Paper1.tex` | Skeleton | Sections to be written |
| Conference slides | `Slides/Paper1_slides.tex` | Not started | Beamer |
| Slides (Quarto) | `Quarto/Paper1_slides.qmd` | Not started | RevealJS |

## Research Design (quick reference)

| Element | Value |
| --- | --- |
| Identification | Demand shock = price change in 10:59–11:15 auction window (Lengyel & Giuliodori 2022) |
| Instrument | Change in Bank of Italy bid-to-cover ratio (ΔBTC\_BoI) |
| Sample | No-syndication BTPs; maturities 3Y, 5Y, 10Y, 15Y, 30Y |
| Estimators | OLS-HAC (L=1 baseline), Huber-T |
| Comovement | Corporate/private debt + equities (Panels A & B) |
| Exogeneity | Fundamentals/policy expectations don't react (Panel C) |
| Target journals | Journal of Quantitative Finance · Journal of Financial Markets · Journal of Fixed Income |
