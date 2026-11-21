# Staging basics (TNM)

General knowledge, not from one paper. Used by [[RECAP]] and [[thesis-plan]].

## The three letters

| Letter | Question | Values |
|---|---|---|
| **T** (Tumour) | How deep has the tumour grown into the bowel wall? | T1 (shallow) → T4 (through the wall, or into other organs) |
| **N** (Nodes) | Has it reached the nearby lymph nodes? | N0 (no) → N1, N2 (yes, more and more nodes) |
| **M** (Metastasis) | Has it spread to far organs (liver, lungs…)? | M0 (no), M1 (yes) |

## From TNM to stage

| Stage | Rule |
|---|---|
| I–II | No nodes, no metastasis (N0 M0). Only the depth (T) differs |
| III | Lymph nodes touched (N1–N2), no metastasis |
| IV | Metastasis (M1), whatever T and N are |

That's why my three classes are **I–II / III / IV**: the borders are **nodes** and **metastasis**.

## TNM is clinical data

- TNM is recorded by doctors (scans, surgery, microscope), so it is **clinical**, not genomic.
- **cTNM** (clinical): decided before surgery, from scans and exams.
- **pTNM** (pathological): decided after surgery, from the removed tumour under the microscope. More precise.
- TCGA has the pathological one: `PATH_T_STAGE`, `PATH_N_STAGE`, `PATH_M_STAGE`, and the stage `AJCC_PATHOLOGIC_TUMOR_STAGE`.

## AJCC

- **AJCC** = American Joint Committee on Cancer, the group that writes the official staging book.
- Editions: 5th 1997, 6th 2002, 7th 2009, 8th used from 2018. TCGA patients were staged with the older editions.
- What changed in the 8th for colorectal: see Weiser in [[RECAP]].

## SEER stages vs AJCC (from Siegel)

| SEER stage | Meaning | ≈ AJCC |
|---|---|---|
| Localized | Still only in the bowel wall | I–II |
| Regional | Nearby lymph nodes or nearby tissue | III (plus a few stage II) |
| Distant | Far organs (liver, lungs…) | IV |
