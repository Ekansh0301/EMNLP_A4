# When Does the Machine Judge Fail? 

## An Empirical Study of LLM-as-a-Judge for English–Hindi Machine Translation

**Course:** Evaluation Methods in NLP, 2026  
**Author(s):** [Author Name(s)]

---

## Overview

This project investigates the reliability of Large Language Models (LLMs) as automated evaluators for machine translation, using English-to-Hindi translation as a case study. Rather than assuming LLM-based evaluation is inherently good or bad, we treat the judge itself as an object of empirical investigation, systematically examining when it succeeds, when it fails, and why.

### Key Findings

- **Rubric-based evaluation** achieves the highest human–judge correlation (Spearman $r_s = 0.460$, p < 0.001)
- **The judge is robust to adversarial manipulation** — not fooled by fluency-only or meaning-shift attacks
- **Chain-of-thought prompting degrades judgment quality** (contrary to general-purpose LLM findings)
- **Human inter-annotator agreement is very low** ($\bar{\kappa} = 0.132$) on challenging inputs, complicating interpretation of LLM–human correlations
- **Surface quality bias** (penalizing correct-but-rough translations) is the dominant failure mode, not fluency over-trusting

---

## Repository Structure

```
.
├── Paper.pdf                          # Final EMNLP-style paper (6-8 pages)
├── code.ipynb                         # Full analysis pipeline (reproducible)
├── README.md                          # This file
├── references.bib                     # BibTeX citations for all references
├── LATEX_FIXES_SUMMARY.md             # Documentation of formatting fixes
├── compile.sh / compile.bat           # LaTeX compilation scripts
│
├── data/
│   ├── raw/
│   │   └── MT_Human_Eval_Responses - Form Responses 1.csv
│   │       (Raw Google Form export from 3 annotators)
│   │
│   ├── processed/
│   │   ├── human_eval_responses.csv   # Cleaned human evaluations
│   │   ├── gold_standard.csv          # Human consensus gold standard
│   │   └── master_scores.csv          # Consolidated data with all scores
│   │
│   ├── llm_evaluations/
│   │   ├── llm_direct_scores.csv      # Direct 1-5 scoring by Gemini
│   │   ├── llm_pairwise.csv           # Pairwise A/B comparisons
│   │   ├── llm_rubric_scores.csv      # Rubric-based JSON responses
│   │   └── llm_selfpref_scores.csv    # Self-preference bias analysis
│   │
│   └── analysis/
│       ├── llm_prompt_sensitivity.csv # 4 prompt variants × 50 examples
│       ├── correlation_results.csv    # Spearman/Kendall with human gold
│       └── adversarial_results.csv    # 12 adversarial test results
│
└── plots/
    ├── meta_evaluation_plots.png      # Correlations, bias, scatter plots
    ├── failure_mode_plots.png         # 6-way taxonomy distribution
    └── cot_vs_direct_plots.png        # Chain-of-thought vs direct scoring
```

---

## Dataset & Experimental Design

### Part 1: Controlled Benchmark
- **Task:** English-to-Hindi Machine Translation
- **Source:** FLORES-200 devtest split (professionally translated)
- **Examples:** 50 curated sentences across 5 challenge categories
- **Systems:** Qwen3-32B (System A) vs. GPT-OSS-20B (System B)

**Challenge Categories (10 examples each):**
1. **Ambiguous** — Lexical polysemy, pronoun ambiguity, scope ambiguity
2. **Hallucination-prone** — Dense entities, dates, percentages
3. **Noisy inputs** — Typos, code-switching (Hinglish), OCR corruption
4. **Negation/quantifier traps** — Modal verbs, frequency quantifiers, scalar expressions
5. **Long and complex** — 22+ words with subordinate/relative clauses

### Part 2: Human Evaluation (Gold Standard)
- **Annotators:** 3 native/near-native Hindi speakers
- **Dimensions:** Fluency (1–5) & Adequacy (1–5) + Overall Preference
- **Rubric:** Provided with scoring anchors
- **Agreement:** 
  - Krippendorff's α (ordinal): mean = -0.107 (negative = systematic disagreement)
  - Cohen's κ (preference): mean = 0.132 (slight agreement per Landis & Koch)

### Part 3: LLM as Judge
- **Judge:** Gemini 2.0 Flash Lite (Google DeepMind)
- **Three Evaluation Protocols:**
  1. **Direct scoring** — Single 1–5 integer response
  2. **Pairwise comparison** — A vs. B with order swap (for position bias)
  3. **Rubric-based** — JSON output with 4 criteria (adequacy, fluency, terminology, style)

- **Prompt Sensitivity Study** — 4 variants:
  - Verbose expert persona
  - Standard with reference
  - No reference
  - Minimal one-line

### Part 4: Meta-Evaluation
- **Metrics:** Spearman & Kendall correlation vs. human gold
- **Bias Analysis:** 
  - Position bias (order consistency: 86%)
  - Verbosity bias (r_s = -0.161, not significant)
  - Reference bias (paired t-test: no difference)
  - Self-preference bias (N/A — judge ≠ MT systems)

### Part 5: Adversarial Testing
- **12 adversarial examples** across 3 types:
  - Fluent-but-wrong (semantic inversions)
  - Correct-but-poor (meaning preserved; unnatural surface)
  - Meaning-shift (plausible paraphrase with altered truth conditions)
- **Result:** Judge not fooled by any example (mean adversarial score: 1.92 vs. reference: 5.00)

### Part 6: Failure Mode Taxonomy
- **Aligned** (55%) — Score gap ≤ 0.5 points
- **Mild disagreement** (26%) — 0.5–1.5 point discrepancies
- **Surface quality bias** (10%) — Under-penalizes semantically correct but rough output
- **Quantifier blindness** (4%) — Fails to detect subtle scope changes
- **Position sensitivity** (4%) — Pairwise verdict changes with order
- **Overtrusting fluency** (2%) — Over-rewards fluent but incorrect output (rarest)

### Part 7: Chain-of-Thought Extension
- **Direct scoring:** Spearman r_s = 0.440
- **CoT prompting:** Spearman r_s = 0.386 (6% degradation)
- **Category breakdown:** CoT severely underperforms on ambiguous examples (0.276 vs. 0.405)
- **Interpretation:** Reasoning drift + criterion reweighting harm judgment quality

---

## How to Use

### Requirements
- Python 3.8+ (for Jupyter notebook analysis)
- LaTeX/pdflatex (for recompiling the paper)
- Git (for version control)

### Running the Analysis
```bash
# Open the Jupyter notebook
jupyter notebook code.ipynb

# Or use VS Code / JupyterLab
jupyter lab code.ipynb
```


## Data Files Guide

### Raw Data (`data/raw/`)
- `MT_Human_Eval_Responses - Form Responses 1.csv`
  - Raw Google Form responses from 3 annotators
  - Contains: adequacy (1–5), fluency (1–5), preference (A/B/Tie) for 50 examples × 2 systems
  - 200 total rows (50 examples × 2 systems × 3 annotators + header)

### Processed Data (`data/processed/`)
- `human_eval_responses.csv` — Cleaned/pivoted human responses
- `gold_standard.csv` — Aggregated human consensus (mean scores + majority vote)
- `master_scores.csv` — All scores consolidated (human + LLM metrics + source text, reference, outputs, BLEU)

### LLM Evaluations (`data/llm_evaluations/`)
- `llm_direct_scores.csv` — Gemini direct 1–5 scores (100 rows: 50 examples × 2 systems)
- `llm_pairwise.csv` — Pairwise A/B judgments with order swap results
- `llm_rubric_scores.csv` — JSON-parsed rubric scores (4 dimensions)
- `llm_selfpref_scores.csv` — Self-preference bias results (applicable only if judge generated translations)

### Analysis (`data/analysis/`)
- `llm_prompt_sensitivity.csv` — Variant scores (4 variants × 50 examples = 200 rows)
- `correlation_results.csv` — Spearman/Kendall correlations, p-values
- `adversarial_results.csv` — 12 adversarial examples with human & LLM scores

### Visualizations (`plots/`)
- `meta_evaluation_plots.png` — 6-panel overview (correlations, scores by category, bias analysis)
- `failure_mode_plots.png` — Taxonomy distribution and category breakdown
- `cot_vs_direct_plots.png` — Comparison of chain-of-thought vs. direct scoring

---

## Key Files

| File | Purpose |
|------|---------|
| `Paper.pdf` | Final 6–8 page EMNLP-style report |
| `code.ipynb` | Reproducible analysis (all computations, plots, correlations) |
| `references.bib` | BibTeX entries (all citations used in paper) |
| `data/processed/master_scores.csv` | Central data file for reproduction |

---

## Reproducibility

All analysis is self-contained in `code.ipynb`:
- Loads CSV data from `data/` folder
- Computes Spearman/Kendall correlations
- Generates all plots (saved to `plots/`)
- Produces failure mode taxonomy

To reproduce:
1. Install dependencies: `pip install pandas numpy scipy matplotlib seaborn`
2. Open `code.ipynb` in Jupyter
3. Run all cells in order

---

## Citation

If you use this work, please cite:

```bibtex
@article{author2026judge,
  title={When Does the Machine Judge Fail? An Empirical Study of LLM-as-a-Judge for English--Hindi Machine Translation},
  author={[Author Name(s)]},
  year={2026},
  journal={Evaluation Methods in NLP},
  note={Course project submission}
}
```

---

## Limitations

- **Single judge model:** Findings may not generalize to other LLMs (GPT-4, Llama, etc.)
- **Single language pair:** English-to-Hindi is morphologically rich; results may not transfer to other pairs
- **Coarse adversarial manipulations:** One semantic error per example; subtler perturbations not tested
- **Limited annotators:** 3 annotators; higher counts would yield more stable agreement

---

## Future Directions

1. **Multi-model comparison:** Test with GPT-4, Llama-3, Claude, others
2. **Cross-lingual evaluation:** Other morphologically rich target languages (Japanese, Arabic, Turkish)
3. **Subtler adversarial perturbations:** Coreference errors, pragmatic implicature violations, register shifts
4. **Automated failure detection:** Can we predict when the judge will fail?
5. **Calibration methods:** Post-hoc score adjustment to align better with human consensus

---

## Contributors

- **Human Annotators:** Gupt, AKG, Bhuvan Kapur
- **Analysis:** [Author Name(s)]
- **Course:** Evaluation Methods in NLP, 2026
- **Institution:** [University Name]

---

## Questions?

Refer to:
- **Paper:** `Paper.pdf` (full 6–8 page report)
- **Code:** `code.ipynb` (all computations)
- **Data:** `data/` folder (all intermediate files)
- **Plots:** `plots/` folder (visualizations)

---

**Submission Deadline:** May 3, 2026 ✓ Completed  
**Status:** Ready for publication
