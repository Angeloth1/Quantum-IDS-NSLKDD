# QIDS — Branch: `Quantum-encoding-amplitude`

> **You are on the `Quantum-encoding-amplitude` branch.** This is the **earliest iteration** of the 22-class, in-sample multiclass experiment — the experiment that first produced the **9% mode-collapse result** later consolidated on the [`classificationFaild` branch](../../tree/classificationFaild) and reported in §8 of the paper.
>
> This branch is preserved primarily **for provenance / history**. For the overall project and a map of all branches, start at the [`main` branch README](../../tree/main).

---

## 📖 What this branch contains

This branch holds the first working version of the amplitude-encoding multiclass pipeline:

- A **class-balanced** training set spanning the **22 raw NSL-KDD attack categories** (200 samples per class, oversampled with replacement).
- The 4-qubit amplitude-encoded VQC trained on that balanced set.
- **In-sample** evaluation (on the training data itself), to probe representational capacity.

Functionally, the result here is **equivalent** to the one on `classificationFaild`: the VQC collapses to ~9% in-sample accuracy with predictions concentrated on a tiny subset of classes. The `classificationFaild` branch is the cleaner, citable version of the same experiment; this branch is kept so the development history is not lost.

---

## 🎯 Key result

Same degenerate mode collapse as the headline finding: **~9% in-sample accuracy** on the 22-class balanced set, with only a couple of classes receiving any non-zero recall and all others at zero. See the [`classificationFaild` README](../../tree/classificationFaild) for the full per-class breakdown and the paper's §8 for interpretation.

---

## ▶️ How to reproduce

```bash
git checkout Quantum-encoding-amplitude
# install dependencies (see main branch README for the full environment)
jupyter lab
```

Run the notebook(s) in `notebooks/` end to end.

> Note: this branch is referenced at its current **HEAD** and is kept for provenance. If you want the canonical version of this experiment, use [`classificationFaild`](../../tree/classificationFaild). Please do not modify this branch after submission.

---

## ⚠️ Known issue (transparency note)

This branch still contains the original label-binarization bug: the Target Encoder is fit using a check for `'normal.'` (with a trailing period) that does not match this dataset's labels (`'normal'`, no period). As on the other pre-fix branches, this affects only the Target Encoder's intermediate binary label and **not** the final VQC training labels, and is corrected on `main`. It does not affect the reported ~9% figure.
