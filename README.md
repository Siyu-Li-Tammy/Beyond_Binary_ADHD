# HYPERAKTIV-ML

Code accompanying an anonymous submission, **"Beyond Binary ADHD Classification: Multimodal Cognitive Phenotyping in a Clinical Psychiatric Cohort,"** submitted to [MultiPsyche 2026](https://sites.google.com/view/multipsyche/call-for-papers) (First Workshop on Multimodal, Multilingual, and Multicultural Mental Health and Psychotherapy).

A machine learning pipeline for adult ADHD classification and phenotyping on the public [HYPERAKTIV dataset](https://datasets.simula.no/hyperaktiv/) (Hicks et al., 2021), fusing four modalities — CPT-II neuropsychological testing, wrist actigraphy, heart-rate variability (HRV), and self-report questionnaires (ASRS, WURS, MADRS, HADS-A/D) — across binary classification, three-way classification, and unsupervised cognitive-phenotype clustering.

This repository contains only the analysis code; the manuscript itself is submitted separately through the workshop's review system.

## Headline findings

| Task | Best modality block | Key result |
|---|---|---|
| Binary classification (ADHD vs. non-ADHD clinical controls) | CPT-II + Self-report | AUC = 0.908, balanced accuracy = 0.795, permutation p = 0.002, n = 97 |
| Three-way classification (non-inattentive ADHD / ADD / non-ADHD) | All modalities | macro-F1 = 0.630, macro-AUC = 0.758; ADD recall = 0.524 (the lowest of the three classes, and a bottleneck across every modality block) |
| Clustering | CPT-II | k = 4 cognitive-severity gradient; clusters generally align more closely with CPT-derived cognitive structure than with the binary ADHD diagnosis label |

Physiological modalities alone — HRV (AUC = 0.429) and actigraphy (AUC = 0.594) — perform near or below chance. We attribute this to HYPERAKTIV's use of psychiatric (not healthy) controls: comorbid mood/anxiety/substance-use disorders in the control group share autonomic and motor signatures with ADHD. Full statistics, effect sizes, and discussion are in the accompanying manuscript.

## Repository structure

```
.
├── README.md
├── requirements.txt
└── HYPERAKTIV_ML_pipeline.ipynb            ← main, canonical notebook (Phases 0–7)

```

## Pipeline overview (`notebooks/HYPERAKTIV_ML_pipeline.ipynb`)

| Phase | Description |
|---|---|
| 0 | Install dependencies, fix random seeds (`SEED = 42`) |
| 1 | Load & clean CPT-II, actigraphy, HRV, self-report, and patient-info tables; build per-modality participant overlap sets |
| 2 | Binary classification across modality combinations (Logistic Regression / Random Forest / XGBoost / LightGBM, 10-fold stratified CV, dummy baselines, 500-iteration permutation tests) |
| 3 | Three-way classification (non-inattentive ADHD / ADD / non-ADHD) with confusion-matrix and per-class recall analysis |
| 4 | Per-modality unsupervised clustering (K-Means, agglomerative), select-k diagnostics, ARI against clinical labels and against CPT-derived cognitive clusters |
| 5–7 | Publication figures, cross-modality cluster agreement, optional PDF export |

## Setup

```bash
python -m venv .venv && source .venv/bin/activate   # or conda/uv, your preference
pip install -r requirements.txt
```

**Data access:** the notebook downloads data at runtime via `kagglehub` from the [`arashnic/adhd-diagnosis-data`](https://www.kaggle.com/datasets/arashnic/adhd-diagnosis-data) Kaggle mirror of HYPERAKTIV. You need a free Kaggle account with an API token:
1. Kaggle → Account Settings → *Create New Token* → downloads `kaggle.json`
2. Place it at `~/.kaggle/kaggle.json`, **or** export `KAGGLE_USERNAME` / `KAGGLE_KEY` as environment variables.

Then open `notebooks/HYPERAKTIV_ML_pipeline.ipynb` and run top to bottom.

> The original HYPERAKTIV data can also be obtained directly from [Simula](https://datasets.simula.no/hyperaktiv/) or [OSF](https://osf.io/3agwr) if you prefer not to use the Kaggle mirror; you'll need to point `DATA_PATH` in Phase 1 at your local copy instead.

## Reproducibility notes

- All seeds fixed globally (`random`, `numpy`, `PYTHONHASHSEED`).
- Statistical significance per modality block confirmed via 500-iteration permutation testing on balanced accuracy — reported as `p = 0.002` (the floor achievable with 500 iterations), not `p < 0.001`.
- Raw CPT-II scores are used for classification (T-scores are pre-normalized toward the ADHD group and would inflate apparent separation); T-scores are used for clustering (needed to put dimensions on a common scale for Euclidean K-Means).
- This notebook was developed and executed in Google Colab against the live Kaggle mirror. It has been syntax-checked in this repository update but **not re-executed end-to-end** in an offline environment — re-run it in your own environment before citing new numbers, and treat existing cell outputs as the last verified run.

## Dataset & ethics

HYPERAKTIV (Hicks et al., 2021) contains activity, heart-rate, and neuropsychological data from 51 adults with ADHD and 52 clinical (not healthy) controls, released under CC BY-NC 4.0 for research and educational use. This project performs secondary analysis only — no new human-subjects data was collected. See the accompanying manuscript for the full Ethical Considerations and Limitations sections.

## Citation

If you use this code, please cite the original dataset:

```bibtex
@inproceedings{Hicks2021,
  title     = {{HYPERAKTIV: An Activity Dataset from Adult Patients with Attention-Deficit/Hyperactivity Disorder (ADHD)}},
  author    = {Hicks, Steven and Stautland, Andrea and Fasmer, Ole Bernt and F{\o}rland, Wenche and
               Hammer, Hugo Lewi and Halvorsen, P{\aa}l and Mjeldheim, Kristin and Oedegaard, Ketil Joachim and
               Osnes, Berge and Syrstad, Vigdis Elin Gi{\ae}ver and Riegler, Michael and Jakobsen, Petter},
  booktitle = {Proceedings of the 12th ACM Multimedia Systems Conference (MMSys '21)},
  year      = {2021},
  doi       = {10.1145/3458305.3478454}
}
```

A citation for the accompanying paper will be added once the anonymous review period has concluded.

## License

Code in this repository: MIT (adjust as you prefer). The HYPERAKTIV dataset itself is CC BY-NC 4.0 — commercial use requires permission from the original authors.
