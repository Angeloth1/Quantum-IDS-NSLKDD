# Quantum Intrusion Detection System (QIDS): Evaluating VQCs in the NISQ Era

This repository contains the source code, data preprocessing pipelines, and experimental results supporting the paper *"Evaluation of Variational Quantum Classifiers (VQC) for Cyberattack Detection in the NISQ Era"* (Thomos & Andronikos, submitted to *J. Sens. Actuator Netw.*).

> **Reproducibility note (read this first):** the experiments reported in the paper were developed iteratively across several branches of this repository, each isolating a different methodological question (data balancing strategy, number of attack categories, evaluation protocol). The table in [Branch-to-Experiment Map](#branch-to-experiment-map) below tells you exactly which branch reproduces which table/figure in the paper. The `main` branch is the curated, default view of the repository and contains the binary-classification pipeline (Sections 3–7 of the paper) plus a pointer to the multiclass branches; it intentionally does **not** duplicate the multiclass code to avoid divergent copies of the same pipeline.

---

## 📖 Overview

As network-based attacks grow in complexity, classical Intrusion Detection Systems (IDS) face continuous challenges. While QML offers theoretical exponential advantages through high-dimensional Hilbert spaces, the physical realities of the Noisy Intermediate-Scale Quantum (NISQ) era impose strict hardware constraints.

This project isolates the **Quantum Bottleneck** caused by Amplitude Encoding. By compressing 16 classical features into a restricted 4-qubit register and benchmarking two distinct optimizers (COBYLA and SPSA), we evaluate whether performance ceilings are artifacts of the optimization landscape or fundamental limits of the geometric encoding itself.

### Key Findings

- **Binary VQC Performance:** Both deterministic (COBYLA) and stochastic (SPSA) optimizers plateaued at **~88% accuracy** in binary classification (Normal vs. Attack) on a held-out test set (5,037 records). See `main` branch, `notebooks/MCC.ipynb`.
- **Optimization vs. Expressivity:** The identical convergence of two fundamentally different optimizers indicates the performance ceiling is an architectural limit of the 4-qubit amplitude compression, not a failure to escape local minima.
- **Classical Parity Benchmark:** A highly constrained classical neural network (a "Tiny MLP," 4 hidden nodes) achieved **97% accuracy** under the same compressed input, highlighting the expressiveness gap caused by linear quantum unitary transformations under extreme compression.
- **Multiclass Mode Collapse (headline finding):** When trained on a class-balanced set spanning the **22 raw NSL-KDD attack categories** (200 oversampled records per class) and evaluated **in-sample** — i.e., on the very data the model was trained on — the VQC collapsed to **9% accuracy** (only 2 of 22 classes received any non-zero recall, both with precision ≈0.10). Because the model fails even to fit its own training data, this is evidence of a representational/capacity bottleneck rather than a generalization failure. See `classificationFaild` branch.
- **Robustness checks (secondary, not headline):** Two complementary multiclass configurations are kept as supporting evidence rather than the primary claim:
  - `Quantum-encoding-amplitude` branch: same 22-class, in-sample setup that produced the 9% result above (this is the branch where the experiment was first run).
  - `SMOTE` branch: the 4 grouped attack families (DoS/Probe/R2L/U2R) with SMOTE oversampling, evaluated on a genuine held-out subset (400 records) — accuracy 94% overall but with 0% recall on the minority R2L/U2R classes due to their near-absence in the held-out subset. This result is reported in the paper as a contextual robustness check, not as contradicting the 9% headline finding, since it uses a different (coarser, 4-class) label granularity and a different evaluation protocol.

---

## 🌳 Branch-to-Experiment Map

The repository's history reflects an iterative research process. Rather than rewrite history or force-merge divergent experimental setups into a single linear branch (which would risk silently changing the conditions under which each reported number was produced), each major methodological variant is preserved on its own branch. **This is intentional and documented, not an oversight.**

| Branch | Paper section(s) | What it contains | Key result |
|---|---|---|---|
| [`main`](../../tree/main) | §2–7 (Implementation, Preprocessing, Circuit Design, Binary Results) | Canonical, cleaned preprocessing pipeline (`main.ipynb`) and binary VQC training/evaluation (`MCC.ipynb`), plus circuit visualization (`modelCircuitDesing.ipynb`). Label binarization bug from earlier branches (see below) is fixed here. | Binary VQC: 88% (COBYLA & SPSA); Tiny MLP: 97% |
| [`classificationFaild`](../../tree/classificationFaild) | §8 (Multiclass Mode Collapse) — **primary source for the 9% headline result** | 22-raw-class balanced training set (200 samples/class, oversampled), in-sample evaluation. Also contains the binary pipeline at an earlier pre-fix state (see Known Issues). | Multiclass: 9% accuracy (in-sample), 22 raw classes |
| [`Quantum-encoding-amplitude`](../../tree/Quantum-encoding-amplitude) | §8 (first iteration of the multiclass experiment) | Earliest version of the 22-class balanced/in-sample multiclass experiment; functionally equivalent to the result on `classificationFaild`. Kept for provenance/history. | Multiclass: 9% accuracy (in-sample), 22 raw classes |
| [`SMOTE`](../../tree/SMOTE) | §8 (robustness check, 4-class grouping) | 4 grouped attack families (DoS/Probe/R2L/U2R), SMOTE oversampling, genuine held-out test subset (400 records). | Multiclass: 94% overall, 0% recall on R2L/U2R |

**Known issue (fixed in `main`, present in earlier branches):** the categorical Target Encoder on `Quantum-encoding-amplitude`, `classificationFaild`, and `SMOTE` was fit using a binarized label check for `'normal.'` (with a trailing period), which does not match this dataset's actual label format (`'normal'`, no period). This affects only the Target Encoding step's intermediate binary label, not the final VQC training labels (which use a separately, correctly computed binarization later in the same notebooks). It is fully corrected on `main`. We flag this here for full transparency; it does not affect any of the accuracy figures reported in the paper, since the downstream VQC/MLP training and evaluation use the corrected binarization.

---

## ⚙️ Methodology & Architecture

### 1. Data Preprocessing

- **Dataset:** NSL-KDD (20% Research Subset).
- **Target Encoding:** Replaced sparse categorical features (protocol type, payload size category) with semantic risk probabilities to ensure "Quantum Economy" and avoid the dimensionality explosion of One-Hot Encoding.
- **Dimensionality Reduction:** Classical PCA was used to extract exactly 16 principal components (retaining ~86.78% variance).
- **Normalization:** Strict L2 normalization was applied to satisfy the quantum probability conservation postulate ($\sum |x_i|^2 = 1$), a prerequisite for Amplitude Encoding.

### 2. Quantum Circuit Design

- **State Preparation:** Amplitude Encoding mapping 16 PCA components into the probability amplitudes of 4 qubits ($2^4 = 16$).
- **Trainable Ansatz:** `RealAmplitudes` architecture with 5 repetitions (`reps=5`), yielding 24 trainable parameters for the binary classifier reported in §7. Earlier exploratory iterations on non-`main` branches used `reps=2` or `reps=3` (28 or higher parameter counts) before this was standardized; see branch-specific notebooks for exact configuration if reproducing an intermediate result.
- **Measurement:** Z-basis measurement with a parity mapping function collapsing the 4-bit probability distribution into a binary classification outcome.

### 3. Multiclass Evaluation Protocol

Unlike the binary classifier (§7), which is evaluated on a genuine held-out test set, the headline multiclass result (§8, 9% accuracy) is deliberately evaluated **in-sample** — on the same balanced training data the model was fit on — in order to isolate representational capacity from generalization. A model that cannot fit even its own training data under amplitude encoding demonstrates a structural bottleneck independent of any train/test split. The `SMOTE` branch additionally reports a held-out evaluation under a coarser 4-class grouping for context; see the [Branch-to-Experiment Map](#branch-to-experiment-map) for how these relate.

---

## 📂 Repository Structure

```
├── data/                   # Raw and preprocessed NSL-KDD datasets (.txt, .csv) — not committed, see Installation
├── models/                 # Saved VQC (dill) and classical models
├── npz/                    # Serialized L2-normalized numpy arrays for fast loading
├── notebooks/              # Jupyter notebooks containing the core logic (contents vary by branch — see map above)
├── README.md               # This file
```

---

## 🚀 Installation & Reproducibility

**Prerequisites:**
- Python 3.10+
- Qiskit (and `qiskit-aer` for statevector simulation)
- `qiskit-machine-learning`, `qiskit-algorithms`
- Scikit-Learn, Pandas, NumPy, Matplotlib, Seaborn
- `imbalanced-learn` (only required on the `SMOTE` branch)

**Step 1: Repository Setup**

```bash
# Clone the repository
git clone https://github.com/Angeloth1/Quantum-IDS-NSLKDD.git
cd Quantum-IDS-NSLKDD

# Checkout the branch matching the result you want to reproduce (see table above), e.g.:
git checkout classificationFaild

# Install required dependencies
pip install qiskit qiskit-aer qiskit-machine-learning qiskit-algorithms scikit-learn pandas numpy matplotlib seaborn dill imbalanced-learn
```

**Step 2: Dataset Acquisition**

The NSL-KDD dataset is not hosted directly in this repository.
1. Visit the Kaggle repository: [NSL-KDD Dataset by Hassan](https://www.kaggle.com/datasets/hassan06/nslkdd)
2. Download the archive and extract it.
3. Place the `KDDTrain+_20Percent.txt` file inside the `data/` directory of this project.

---

## 📌 Reproducibility

Each result in the paper is reproduced by checking out the corresponding branch (see the [Branch-to-Experiment Map](#branch-to-experiment-map) above) and running its notebooks. The experiments live at the **head of each branch** — there is no separate pinned tag or commit to navigate to. Simply:

```bash
git checkout <branch-name>   # e.g. classificationFaild for the 9% multiclass result
```

> **Note:** because the reported figures correspond to the current head of each branch, the branches should not be modified after submission if the published numbers are to remain reproducible.

---

## Conflicts of Interest

The authors declare no conflicts of interest.
