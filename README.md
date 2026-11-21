# Intestinal Cancer Staging with Machine Learning

This repository is part of a thesis on **intestinal/colorectal cancer
staging and survival prediction using machine learning**.

This is the real name of the thesis **Staging and explainability (features) from multi modal Data (clinical and genomic) for colorectal cancer.**

I will be using TCGA data, 594 patients, it comes from cBioPortal. I'm gonna predict the stage and the survival (PFI and OS). For the staging prediction I can't use TNM, so I will be using clinical and genomic features. For the survival I will compare stage alone with stage + clinical + genomic data.

SHAP is here to tell us how much each feature pushes the prediction up or down, so which features the model uses. SHAP talks about the whole model and about one patient. My goal is to see if the genes (KRAS, BRAF, MSI…) bring information about the stage and the survival.

## Introduction

Colorectal cancer is one of the most common cancers worldwide. Its
prognosis strongly depends on the stage at diagnosis, usually described
using the TNM staging system (AJCC).

This project explores whether machine-learning algorithms can support
the analysis of colorectal cancer data by predicting:

- the cancer stage;
- patient survival outcomes.

The project begins with a literature review. Later, publicly available
datasets will be used to test algorithms covered during the course.
The objective is not to build a clinically reliable diagnostic tool,
but to understand how these methods behave on real cancer data and to
discuss their strengths and limitations.

## Current focus

At this stage, the project focuses on collecting and organizing
relevant scientific literature on:

- Colorectal cancer staging (TNM, AJCC).
- Machine-learning models for cancer staging and survival prediction.
- The use of clinical and genomic data.
- Evaluation methods and limitations of predictive models in oncology.

## Repository structure

```text
intestinal-cancer-staging-ml/
├── README.md                 # Project overview and roadmap
├── literature/
│   ├── references.bib        # BibTeX bibliography (one entry per source)
│   ├── pdf/                  # Downloaded research papers
│   └── notes/                # RECAP.md (one section per source + synthesis), thesis-plan.md, staging-basics.md
└── data/                     # Not in the repository (TCGA download, about 2.4 GB)
```

## Roadmap

- [x] Define the thesis topic and initial scope.
- [x] Create the repository structure.
- [x] Collect relevant research papers.
- [x] Write concise notes for each source.
- [x] Build a complete BibTeX bibliography.
- [x] Select suitable public datasets.
- [ ] Apply preliminary machine-learning algorithms.
- [ ] Report and discuss the first results.

## Current decisions

- **Dataset:** TCGA colorectal adenocarcinoma, PanCancer Atlas release, from
  cBioPortal (`coadread_tcga_pan_can_atlas_2018`, 594 patients). SEER is
  background only for now. The data are not in this repository (too large);
  download them from https://www.cbioportal.org/study/summary?id=coadread_tcga_pan_can_atlas_2018
  (downloaded October 2026).
- **Target:** AJCC pathological stage in three classes — I–II (324 patients),
  III (170), IV (86); 14 patients without a stage are excluded.
- **Predictors:** clinical and genomic features that are not part of the staging
  definition. The pathological T, N and M categories are never used as inputs.
- **Survival:** PFI (main) and OS (second). Here stage is allowed as an input:
  stage only vs stage + clinical + genomic.

## Next steps

1. Finish the open bibliography items (list in `literature/notes/thesis-plan.md`, "Still to do").
2. Extract the TCGA archive and build the analysis table (clinical + genomic features).
3. Fit baseline models (regularised logistic regression first) under clean cross-validation.
4. Test the course algorithms, explain them with SHAP and document the first results.
