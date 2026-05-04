# When Does the Machine Judge Fail?

## An Empirical Study of LLM-as-a-Judge for English-Hindi Machine Translation

---

## Overview

This project investigates when Large Language Models are reliable evaluators for machine translation, using English-to-Hindi translation as a case study. We treat the judge itself as an object of empirical investigation, systematically examining when it succeeds, when it fails, and why.

### Key Findings

- **Rubric-based evaluation** achieves highest human-judge correlation (Spearman r=0.460, p<0.001)
- **Judge is robust to adversarial attacks** - not fooled by fluency-only or meaning-shift manipulations
- **Chain-of-thought prompting degrades** judgment quality (contrary to general-purpose LLM findings)
- **Surface quality bias** (10%) is dominant failure mode - penalizing correct-but-rough translations
- **Human disagreement is high** (Cohen's kappa=0.132) on challenging inputs, limiting gold standard reliability

---

## What's Included

### Core Files
- **Paper.pdf** - Complete 6-8 page research report with full methodology and results
- **code.ipynb** - Reproducible Jupyter notebook with all analysis and plot generation

### Directory Structure
```
data/
├── raw/                        Raw Google Form responses
├── processed/                  Cleaned & aggregated human evaluations
│   ├── gold_standard.csv      Human consensus scores (mean + majority vote)
│   └── master_scores.csv      All metrics consolidated
├── llm_evaluations/           LLM judge results across 3 protocols
│   ├── llm_direct_scores.csv
│   ├── llm_pairwise.csv
│   └── llm_rubric_scores.csv
└── analysis/                  Meta-evaluation & adversarial results
    ├── correlation_results.csv
    └── adversarial_results.csv

plots/
├── meta_evaluation_plots.png   Correlations & bias analysis (6 subplots)
├── failure_mode_plots.png      Taxonomy distribution
└── cot_vs_direct_plots.png     Chain-of-thought comparison
```

---


## Run the Analysis
```bash
# Install dependencies
pip install pandas numpy scipy matplotlib seaborn jupyter

# Open and run the notebook
jupyter notebook code.ipynb
```

---

## Methodology Summary

### Dataset & Setup
- **Task:** English-to-Hindi Machine Translation (FLORES-200 source)
- **Scale:** 50 curated examples across 5 challenge categories
- **MT Systems:** Qwen3-32B vs. GPT-OSS-20B
- **Challenge Types:** Ambiguous, hallucination-prone, noisy inputs, negation/quantifier traps, long/complex sentences

### Evaluation Methods
1. **Human Gold Standard** - 3 native Hindi speakers scored fluency, adequacy, and overall preference
2. **LLM Judge (Gemini 2.0 Flash Lite)** - Three evaluation protocols:
   - Direct scoring (1-5 scale)
   - Pairwise A/B comparison (with position bias check via order swap)
   - Rubric-based evaluation (4 criteria: adequacy, fluency, terminology, style)
3. **Meta-Evaluation** — Correlation analysis, bias detection, adversarial testing (12 cases)
4. **Chain-of-Thought Study** — Direct prompting vs. multi-step reasoning

### Key Measurements
- Human inter-annotator agreement: Cohen's kappa = 0.132 (very low)
- Rubric-based correlation with human: r = 0.460 (highest)
- Direct scoring correlation with human: r = 0.440
- Adversarial robustness: 0/12 adversarial examples fooled the judge
- Position bias: 86% pairwise consistency

---

## Results Summary

### Correlation with Human Gold
| Evaluation Method | Correlation | p-value | Status |
|-------------------|-------------|---------|--------|
| Rubric-based | 0.460 | <0.001 | Significant |
| Direct scoring | 0.440 | <0.001 | Significant |
| BLEU | 0.153 | 0.128 | Not significant |

### Failure Mode Distribution
- **Aligned** (55%) - Agrees with human judgment
- **Mild disagreement** (26%) - Minor discrepancies
- **Surface quality bias** (10%) - Penalizes rough-but-correct output
- **Quantifier blindness** (4%) - Misses subtle scope changes
- **Position sensitivity** (4%) - Order-dependent judgments
- **Fluency over-trusting** (2%) - Extremely rare

### Adversarial Test Results
All 12 adversarial examples were correctly rejected:
- Fluent-but-wrong: 0 fooled (mean score 2.00 vs. reference 5.00)
- Correct-but-poor: 0 fooled (mean score 1.50 vs. reference 5.00)
- Meaning-shift: 0 fooled (mean score 2.25 vs. reference 5.00)

---

## Data Files Guide

| File | Description |
|------|-------------|
| `data/processed/gold_standard.csv` | Human consensus (mean scores + majority vote) |
| `data/processed/master_scores.csv` | All metrics consolidated |
| `data/llm_evaluations/llm_direct_scores.csv` | Judge's 1-5 scores |
| `data/llm_evaluations/llm_pairwise.csv` | Judge's A/B judgments |
| `data/llm_evaluations/llm_rubric_scores.csv` | Judge's rubric-based scores |
| `data/analysis/correlation_results.csv` | Spearman/Kendall metrics |
| `data/analysis/adversarial_results.csv` | Adversarial test outcomes |

---

## Reproducibility

All analysis is self-contained in `code.ipynb`:
- Loads CSV data from `data/` directory
- Computes Spearman & Kendall correlations with human gold
- Regenerates all plots in `plots/` folder
- Produces failure mode taxonomy

To reproduce all results:
```bash
pip install pandas numpy scipy matplotlib seaborn
jupyter notebook code.ipynb
# Run all cells in order
```

---

## Limitations

- Single judge model (Gemini); findings may not generalize to other LLMs
- Single language pair (English-Hindi); results specific to morphologically rich targets
- 3 human annotators (limited agreement stability)
- Coarse adversarial manipulations (one semantic error per example)

---
