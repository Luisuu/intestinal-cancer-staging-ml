# Thesis plan

My project decisions. Not from one paper; it grows with each paper I read. Basics in [[staging-basics]], paper notes in [[RECAP]].

## Main decisions 

- **Data: TCGA only** for now. Study `coadread_tcga_pan_can_atlas_2018` from cBioPortal, 594 patients, colon + rectum. SEER papers are only related work or for later maybe.
- **Two targets: stage and survival.**
- **Stage = 3 classes: I–II / III / IV** → 324 (55.9%) / 170 (29.3%) / 86 (14.8%); 14 patients have no stage. Why:
  1. Same grouping as Finn (48.9 / 30.1 / 21%), so I can compare.
  2. Simple, meaningful borders: lymph nodes (I–II vs III), metastasis (III vs IV).
  3. These borders are the same in the AJCC 5th, 6th and 7th editions mixed in TCGA; the sub-stages are not.
  4. 4 classes would leave stage I with only 104 patients, and the I vs II border (depth in the wall) is the least likely to show in genes.
- **Kept in reserve:** 2 classes (I–II vs III–IV, to compare with Hu) and 4 classes as a check.

## My gap (what my thesis adds)

Most ML papers predict only one part of stage (N or M), mostly on SEER, without genes. Few predict the full stage: Finn (insurance bills, 3 classes) and Hu (TCGA methylation, 2 classes, with leakage). **No paper in my list does a leakage-free, 3-class, multimodal (clinical + several genomic types) stage model with explanations, plus survival on the same data.**

## SUPER IMPORTANT: what the models may use

| Target | T, N, M, stage allowed as input? | Why |
|---|---|---|
| Stage | ❌ No | Stage is made from them, so it's cheating |
| Survival | ✅ Yes | It's the baseline to beat |

**For stage: no T, N or M as inputs.** Stage is calculated from T, N and M with a fixed rule (N0 M0 → I–II, N+ → III, M1 → IV). If the model gets T, N and M, it just finds that rule again and gets ~100% correct, but learns nothing new. Like predicting a student's final grade when you already give the model the grade. This is **leakage** (Kapoor): a feature that is the answer in disguise.

**For survival: TNM is allowed.** Survival is not calculated from TNM; it happens later. So stage is a legitimate predictor, and the starting point:
- Model 1: survival from stage only (what doctors do today)
- Model 2: stage + clinical + genomic
- Question: does Model 2 do better?

## What the stage model uses instead

- **Clinical:** age, sex, tumour site (colon/rectum), histology
- **Genomic:** MSI, mutations (KRAS, BRAF…), copy number, gene expression

That's the "multimodal" in the title. Explainability (SHAP) then tells which of these features helped.

## Why genetic markers (from Weiser)

AJCC lists MSI, KRAS, NRAS and BRAF as important but leaves them out of the stage. My thesis checks whether they help predict stage and survival.

## Genes I can test (from TCGA 2012 and Weiser)

Checked in my file `data_mutations.txt`: 528 of 594 patients have mutation data.

| Gene | Patients mutated | % | Why |
|---|---|---|---|
| APC | 387 | 73% | Most common; WNT pathway |
| TP53 | 314 | 59% | Very common; linked to aggressiveness (TCGA 2012) |
| KRAS | 218 | 41% | RAS pathway; named by AJCC (Weiser) |
| PIK3CA | 147 | 28% | Linked to aggressiveness (TCGA 2012) |
| FBXW7 | 90 | 17% | Never seen with distant metastasis in TCGA 2012 |
| SMAD4 | 68 | 13% | Common in non-hypermutated tumours |
| BRAF | 62 | 12% | Linked to aggressiveness and worse survival; named by AJCC |
| NRAS | 33 | 6% | RAS pathway; named by AJCC, but rare |

Plus, not single genes:
- **Hypermutated / MSI status**: `SUBTYPE` (MSI, POLE, CIN, GS) or the MSI scores.
- **Tumour location** (right colon / left colon / rectum), from Deng and TCGA 2012.

Note: 66 patients have no mutation data, so I'll have to decide what to do with them (remove, or model without genes).

## Survival target (from Liu CDR)

- **Main: PFI** (`PFS_STATUS`, `PFS_MONTHS`): 150 events, OK for colon and rectum.
- **Second: OS** (`OS_STATUS`, `OS_MONTHS`): 120 deaths. Caution for rectum.
- Not DFI: 371 missing. DSS only as extra (approximated, 24 missing).
- Follow-up is short (median about 21 months): write it as a limit.

### Why PFI (my arguments)

1. **More events = more reliable model.** PFI 150 events, OS 120, DSS 73, DFI 30. With few events, results are unstable, and AJCC disqualifies models with few events.
2. **The TCGA team recommends it** (Liu CDR): PFI is OK for colon *and* rectum. OS is only "caution" for rectum, and about a quarter of my patients are rectal.
3. **Short follow-up (~2 years):** cancer comes back faster than people die, so in 2 years I see more progressions than deaths.
4. **Closer to the tumour:** OS counts death from any cause (heart attack, accident…). Tumour genes can't explain those. PFI is about the cancer itself.

**Possible counter-argument:** OS is more objective (a death date is certain) and more used in clinical papers; PFI depends on when exams were done.
**My answer:** that's why OS is my second analysis. If both give similar results, my conclusions are robust.

Citation note: if I use the `SUBTYPE` column (MSI / CIN / GS / POLE), cite Liu Y 2018 (kept in the bibliography, not read).

## What I take from Finn (stage model)

- **No treatment and nothing from after diagnosis** as input for stage: treatment is chosen *because of* the stage, so it's leakage.
- Expect **stage III** to be the hardest class and **IV** the easiest.
- Report results for **colon and rectum separately** (Finn: rectum much worse).
- Metric: **scaled Brier score** (to compare with Finn), plus per-class AUC and calibration.
- Baseline: **multinomial logistic regression**; random forest was not better in Finn.
- Finn is **not a score to beat**: their features contain the doctors' staging work, mine don't.

## What I take from Hu (genomic side)

- **Choose features only on the training data**, inside cross-validation. Hu chose CpGs on all patients first: leakage.
- Expect **AUC ≈ 0.70** for genomic data alone (2 classes); clinical + genomic does better.
- **CEA** would be a good clinical feature, but it's not in my file. Maybe add it later from TCGA clinical records.

## What I take from Dai (survival model)

- **Baseline to beat:** stage alone gives a C-index of only ~0.60–0.67 (Dai: 0.593 on validation).
- Same design as my Model 1 (stage) vs Model 2 (stage + clinical + genomic): compare the **C-index**, and check calibration.
- Keep the comparison fair: the only difference between Model 1 and Model 2 should be the new clinical/genomic features.
- Dai had 6 years of follow-up, I have ~21 months: fewer events, so expect wider confidence intervals.

## Explainability

- **SHAP only, no LIME.** TreeSHAP (`shap.TreeExplainer`) for tree models; coefficients or linear SHAP for logistic regression.
- Why: SHAP explains one patient *and* the whole model, gives the same result every time, and has a solid theory. LIME only explains one patient and can change between runs.
- Cite Lundberg 2017 (SHAP) + Lundberg 2020 (TreeSHAP).

## Class imbalance (from van den Goorbergh)

- Stage IV is rare (86 of 580). **No SMOTE, no oversampling**: they don't improve AUC and they make the percentages wrong.
- Train on the real distribution; if stage IV is missed, **move the decision threshold**.
- Class weights: not tested by them, may also distort percentages. If used, check calibration.

## Be realistic

MSI is a tendency, not a rule: some MSI-high tumours are still stage III or IV. Predicting stage without TNM is hard; Hu et al. got only ~0.73 AUC for the simpler two-class version. A modest result is still a valid result. Showing which genetic features carry some stage information is the contribution.

## Checklist for the survival model

Use the AJCC rules for a good risk model (Weiser section in [[RECAP]]).

## Still to do for the bibliography

- **Download date** of the cBioPortal data: write the exact date in the thesis.
- **Algorithm references** once the models are fixed: random forest, gradient boosting / XGBoost, penalised logistic regression, random survival forest.
- **Multimodal methods:** a methods review on combining clinical + genomic data (early vs late fusion, feature selection inside cross-validation). Zhou is only an applied example.
- **3-class evaluation:** metrics and calibration for a 3-class target.
- **Survival evaluation:** C-index, time-dependent AUC, integrated Brier score, censoring.
- **Limits of SHAP:** a reference on correlated features and instability.
- **Molecular background:** a review of MSI and KRAS / NRAS / BRAF as prognostic markers.
- **Stage table source:** if I print the table "which T/N/M give which stage", it needs a source (AJCC manual).

