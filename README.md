# AI Reliability in Qualitative Tourism Research

This repository contains the data, code, and synonym lexicon supporting the manuscript:

**Tan, G., Hunt, C. A., & Pan, B. (2026). AI Reliability in Qualitative Tourism Research.** Submitted to *Annals of Tourism Research*.

## Repository Structure

```
├── data/
│   ├── Comparison_AI&JORT_Dec2_2025_v3.xlsx   # Raw experimental data (all 4 experiments)
│   ├── records_unlimited.csv                   # Parsed records: Unlimited Themes experiment
│   ├── records_three_themes.csv                # Parsed records: Three Themes experiment
│   ├── records_scholar.csv                     # Parsed records: Scholar Lens experiment
│   ├── records_drift.csv                       # Parsed records: Drift experiment
│   ├── jort_similarity.csv                     # AI-vs-JORT similarity scores (42 observations)
│   ├── cross_model_agreement.csv               # Cross-model pairwise agreement
│   ├── within_model_agreement.csv              # Within-model same-mode agreement
│   ├── drift_analysis.csv                      # Drift experiment round-by-round metrics
│   ├── scholar_distinctiveness.csv             # Inter-scholar pairwise overlap
│   ├── scholar_jort_bestmatch.csv              # Scholar lens JORT best-match scores
│   ├── scholar_synonym_family_signature.csv    # Scholar vocabulary signatures
│   ├── scholar_vocabulary.csv                  # Scholar vocabulary raw data
│   ├── thematic_breadth.csv                    # Thematic breadth per experiment
│   └── non_compliant_outputs.csv               # Non-compliant AI output log
├── code/
│   ├── REI_Analysis_v2_2026-04-15.ipynb        # Main analysis notebook
│   ├── Figure1_ScholarMatrix_Revised.ipynb     # Figure 2: Inter-scholar distinctiveness heatmap
│   └── Figure3_Drift_Revised.ipynb             # Figure 3: Thematic drift trajectories
├── lexicon/
│   └── synonym_groups.csv                      # Domain-specific synonym lexicon (33 groups, 470 terms)
├── DATA_STRUCTURE_SPEC.md                      # Detailed specification of all data fields
└── README.md
```

## Data Description

Four experiments were conducted using ChatGPT (versions 5 and 5.1) and Claude (Sonnet 4 and 4.5), each in two modes:

1. **Unlimited Themes**: 7 prompting strategies with no constraints on output structure (14 outputs)
2. **Three Themes**: Constrained prompt requiring exactly 3 two-word themes (4 outputs)
3. **Scholar Lens**: 6 scholar-specific prompts across 4 model-mode combinations (24 outputs)
4. **Drift**: 5 tone conditions repeated over 10 rounds across 4 model-mode combinations (220 outputs)

## Similarity Metrics

Three complementary metrics were used throughout:

- **Lexical Jaccard**: Word-level exact overlap
- **Synonym-Aware Jaccard**: Overlap after mapping to domain-specific synonym groups
- **TF-IDF Cosine Similarity**: Morphological and semantic similarity via character n-grams

## Requirements

- Python 3.11+
- pandas, numpy, matplotlib, scikit-learn, scipy, openpyxl

## License

This dataset is provided for academic research purposes.
