# Quantum Intrusion Detection System (QIDS): Evaluating VQCs in the NISQ Era

This repository contains the source code, data preprocessing pipelines, and experimental results for evaluating the structural limits of Variational Quantum Classifiers (VQC) in cybersecurity. Specifically, it investigates the application of Quantum Machine Learning (QML) for network anomaly detection using the **NSL-KDD** dataset.

This research was developed at the Department of Informatics, Ionian University, exploring hybrid quantum-classical computing applications in intrusion detection systems.

---

## 📖 Overview

As network-based attacks grow in complexity, classical Intrusion Detection Systems (IDS) face continuous challenges. While QML offers theoretical exponential advantages through high-dimensional Hilbert spaces, the physical realities of the Noisy Intermediate-Scale Quantum (NISQ) era impose strict hardware constraints. 

This project specifically isolates the **Quantum Bottleneck** caused by **Amplitude Encoding**. By compressing 16 classical features into a restricted 4-qubit register and benchmarking two distinct optimizers (COBYLA and SPSA), we evaluate whether performance ceilings are artifacts of the optimization landscape or fundamental limits of the geometric encoding itself.

### Key Findings
* **VQC Performance:** Both deterministic (COBYLA) and stochastic (SPSA) optimizers plateaued at an **88% accuracy** in binary classification (Normal vs. Attack).
* **Optimization vs. Expressivity:** The identical convergence of two fundamentally different optimizers confirms that the performance ceiling is an architectural limit of the 4-qubit amplitude compression, not a failure to escape local minima.
* **Classical Parity Benchmark:** A highly constrained classical neural network (a "Tiny MLP") utilizing exactly 4 hidden nodes—architecturally analogous to the 4-qubit space—achieved **97% accuracy** using the exact same compressed input, highlighting the expressiveness gap caused by linear quantum unitary transformations under extreme compression.
* **Mode Collapse in Multiclass:** When exposed to a perfectly balanced 4-class dataset (DoS, Probe, R2L, U2R), the VQC suffered a degenerate mode collapse, achieving only **9% accuracy**. This demonstrates that aggressive probability amplitude compression destroys the linear separability of complex, entangled attack vectors.

---

## ⚙️ Methodology & Architecture

### 1. Data Preprocessing
* **Dataset:** NSL-KDD (20% Research Subset).
* **Target Encoding:** Replaced highly sparse categorical features (like protocol type and payload size) with semantic risk probabilities to ensure "Quantum Economy" and avoid the dimensionality explosion of One-Hot Encoding.
* **Dimensionality Reduction:** Classical PCA was utilized to extract exactly 16 principal components (retaining ~86.78% variance).
* **Normalization:** Strict L2 normalization was applied to the feature vectors to satisfy the quantum probability conservation postulate ($\sum |x_i|^2 = 1$), a strict prerequisite for Amplitude Encoding.

### 2. Quantum Circuit Design
* **State Preparation:** Amplitude Encoding mapping 16 PCA components into the probability amplitudes of 4 qubits ($2^4 = 16$).
* **Trainable Ansatz:** `RealAmplitudes` architecture with 5 repetitions (`reps=5`), yielding robust linear entanglement topologies and exactly 24 trainable parameters.
* **Measurement:** Z-basis measurement utilizing a Parity Mapping function to collapse the 4-bit probability distribution into a binary classification outcome.

---

## 📂 Repository Structure

```text
├── data/                   # Raw and preprocessed NSL-KDD datasets (.txt, .csv)
├── models/                 # Saved VQC (dill) and classical models
├── npz/                    # Serialized L2-normalized numpy arrays for fast loading
├── notebooks/              # Jupyter notebooks containing the core logic
├── README.md               # Project documentation
```
## 🚀 Installation & Reproducibility

To ensure strict reproducibility, the environment relies on the IBM Qiskit ecosystem and standard Python data science libraries. 

**Prerequisites:**
* Python 3.10+
* Qiskit (and `qiskit-aer` for statevector simulation)
* `qiskit-machine-learning`, `qiskit-algorithms`
* Scikit-Learn, Pandas, NumPy, Matplotlib, Seaborn

**Step 1: Repository Setup**
```bash
# Clone the repository
git clone [https://github.com/Angeloth1/Quantum-IDS-NSLKDD.git](https://github.com/Angeloth1/Quantum-IDS-NSLKDD.git)
cd Quantum-IDS-NSLKDD

# Install required dependencies
pip install qiskit qiskit-aer qiskit-machine-learning qiskit-algorithms scikit-learn pandas numpy matplotlib seaborn dill
**Step 2: Dataset Acquisition**
For academic integrity and storage constraints, the NSL-KDD dataset is not hosted directly in this repository. You must download it before running the notebooks:
1. Visit the Kaggle repository: [NSL-KDD Dataset by Hassan](https://www.kaggle.com/datasets/hassan06/nslkdd)
2. Download the archive and extract it.
3. Place the `KDDTrain+_20Percent.txt` file inside the `data/` directory of this project.
```
