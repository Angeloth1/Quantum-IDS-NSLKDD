# QIDS — Branch: `SMOTE`

> **You are on the `SMOTE` branch.** This branch contains a **robustness / context check** referenced in §8 of the paper: the multiclass VQC under a **coarser 4-class grouping** (DoS / Probe / R2L / U2R) with SMOTE oversampling, evaluated on a **genuine held-out test subset**.
>
> This is **not** the headline multiclass result. The headline 9% mode-collapse finding uses 22 raw classes and in-sample evaluation and lives on the [`classificationFaild` branch](../../tree/classificationFaild). For the overall project and a map of all branches, start at the [`main` branch README](../../tree/main).

---

## 📖 What this branch contains

This branch explores whether the multiclass picture changes under a **different, easier label granularity** and a **held-out evaluation protocol**:

1. The 22 raw attack categories are grouped into the **4 canonical NSL-KDD families**: DoS, Probe, R2L, U2R.
2. **SMOTE** is applied to oversample the minority families.
3. The trained VQC is evaluated on a **held-out test subset (400 records)** — a genuine generalization test, unlike the in-sample protocol of the headline experiment.

---

## 🎯 Key result (paper §8 — robustness check)

| Metric | Value |
|---|---|
| Overall accuracy (held-out, 4 classes) | **94%** |
| Recall on majority classes (DoS, Probe, normal-adjacent traffic) | high |
| Recall on R2L | **0%** |
| Recall on U2R | **0%** |

The headline 94% is **dominated by the majority classes**. The minority families R2L and U2R are so sparse in the small 400-record held-out subset that the model achieves **0% recall** on them — i.e., the high overall number masks a complete failure on the rarest, most security-critical attack types.

> **How this relates to the headline finding:** this result does **not** contradict the 9% mode collapse on `classificationFaild`. The two use different label granularities (4 grouped families here vs. 22 raw categories there) and different evaluation protocols (held-out here vs. in-sample there). The paper reports this 94% figure as a contextual robustness check — a demonstration that aggregate accuracy under coarse grouping can look healthy while fine-grained / minority-class performance collapses — not as the primary multiclass claim.

---

## ▶️ How to reproduce

```bash
git checkout SMOTE
# install dependencies (see main branch README for the full environment)
jupyter lab
```

Run the notebook(s) in `notebooks/` end to end. The 94% overall accuracy and the per-class recall (including the 0% on R2L/U2R) appear in the final classification report.

> Note: this branch is referenced at its current **HEAD**. Please do not modify this branch after submission so the reported figures remain reproducible.

---

## ⚠️ Known issue (transparency note)

Like the other pre-fix branches, the Target Encoder here is fit using a check for `'normal.'` (with a trailing period) that does not match this dataset's labels (`'normal'`, no period). This affects only the Target Encoder's intermediate binary label, **not** the final VQC training labels, and is corrected on `main`. It does not affect the reported 94% / 0%-recall figures.
