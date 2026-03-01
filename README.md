# SPID: Spectral Picard-Ishikawa-Dykstra Iterations

[![Language](https://img.shields.io/badge/Language-Python_3.9+-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> **Accelerated Computation of the Nearest Correlation Matrix via Spectral Picard-Ishikawa-Dykstra (SPID) Iterations**
> *Oyeyinka E. Ibrahim (2026) — Northern Illinois University*

---

## Overview

The **Nearest Correlation Matrix (NCM)** problem involves projecting a symmetric matrix onto the intersection of the positive semidefinite cone and the affine subspace of unit-diagonal matrices. This arises routinely in quantitative finance when empirical estimates — from asynchronous or missing data — fail to be valid correlation matrices.

**SPID v4** is a novel, memory-efficient primal solver that:

- Wraps the classical Dykstra cycle in an **Ishikawa-style convex combination**
- Applies a safeguarded **Alternating Barzilai-Borwein (ABB) spectral step** to aggressively accelerate convergence
- Maintains a strict $\mathcal{O}(n^2)$ memory footprint — only two additional $n \times n$ matrices beyond the base Dykstra state (vastly lighter than Anderson Acceleration)
- Strictly preserves **exact unit-diagonal feasibility** ($\text{diag}(X) = \mathbf{1}$)

---

## Repository Structure

```
spid/
├── src/
│   └── spid.py                  # Core implementation: spid_v4, MAP baseline, Anderson Acceleration
├── benchmarks/
│   └── run_benchmarks.py        # Reproduces all paper tables and figures
├── LICENSE
└── README.md
```

---

## Requirements

- Python 3.9+
- `numpy`
- `scipy`

```bash
pip install numpy scipy
```

---

## Quick Start

```python
import numpy as np
from src.spid import spid_v4

# Define an invalid empirical correlation matrix (Higham 4×4 example)
G = np.array([
    [ 2.0, -1.0,  0.0,  0.0],
    [-1.0,  2.0, -1.0,  0.0],
    [ 0.0, -1.0,  2.0, -1.0],
    [ 0.0,  0.0, -1.0,  2.0]
])

# Compute the Nearest Correlation Matrix
X_opt, iterations = spid_v4(G, tol=1e-5)

print(f"Converged in {iterations} iterations.")
print("Repaired matrix:")
print(np.round(X_opt, 4))
```

---

## Reproducing Paper Results

Run the benchmark script from the project root to reproduce all numerical experiments and ablation studies from the manuscript:

```bash
python benchmarks/run_benchmarks.py
```

This runs SPID v4 head-to-head against **Higham's MAP baseline** and **Anderson Acceleration** ($m=4$) across two evaluation tracks:

| Track | Description |
|---|---|
| Classical test matrices | Higham Ex 3.1, Toeplitz 6×6 |
| Missing data topologies | 25% MCAR / NMAR missingness on financial return datasets |

**Expected results:** SPID v4 matches MAP's optimal Frobenius distance while requiring ~52–64% fewer iterations, and avoids the collinearity instability that occasionally affects Anderson Acceleration on dense matrices.

---

## Citation

If you use SPID v4 or this codebase in your research, please cite:

```bibtex
@article{ibrahim2026spid,
  title     = {Accelerated Computation of the Nearest Correlation Matrix
               via Spectral Picard-Ishikawa-Dykstra (SPID) Iterations},
  author    = {Ibrahim, Oyeyinka E.},
  journal   = {Submitted for Publication},
  year      = {2026},
  institution = {Northern Illinois University}
}
```

---

## License

This project is licensed under the **MIT License** — see [`LICENSE`](LICENSE) for details.
