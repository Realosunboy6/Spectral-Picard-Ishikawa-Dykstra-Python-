
---

```markdown
# SPID: Spectral Picard-Ishikawa-Dykstra Iterations

[![Language](https://img.shields.io/badge/Language-Python_3.9+-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This repository contains the official Python implementation of the **SPID v4** algorithm, as presented in the paper:
> **Accelerated Computation of the Nearest Correlation Matrix via Spectral Picard-Ishikawa-Dykstra (SPID) Iterations** > *Oyeyinka E. Ibrahim (2026)*

## Overview
The Nearest Correlation Matrix (NCM) problem involves projecting a symmetric matrix onto the intersection of the positive semidefinite cone and the affine subspace of unit-diagonal matrices. This problem arises routinely in quantitative finance when empirical estimates (e.g., from asynchronous or missing data) fail to be valid correlation matrices.

**SPID v4** is a novel, memory-efficient primal solver that:
1. Wraps the classical Dykstra cycle in an Ishikawa-style convex combination.
2. Applies a safeguarded Alternating Barzilai-Borwein (ABB) spectral step to aggressively accelerate convergence.
3. Maintains a strict $\mathcal{O}(n^2)$ memory footprint, requiring only two additional $n \times n$ matrices beyond the base Dykstra state (vastly lighter than Anderson Acceleration).
4. Strictly preserves exact unit-diagonal feasibility ($\text{diag}(X) = 1$).

## Repository Structure
* `src/spid.py`: Contains the core Python implementation of the `spid_v4` algorithm, alongside the baseline Higham Modified Alternating Projections (MAP) and Anderson Acceleration (AA) methods.
* `benchmarks/run_benchmarks.py`: The reproduction script that generates the exact performance tables presented in the manuscript (including the Higham $4 \times 4$, Toeplitz $6 \times 6$, and simulated financial missing-data matrices).
* `README.md`: Project documentation.

## Requirements
* **Python** 3.9 or higher
* Core Packages: `numpy`, `scipy`

You can install the required dependencies using pip:
```bash
pip install numpy scipy

```

## Quick Start

You can easily use SPID v4 in your own Python projects to repair invalid correlation matrices.

```python
import numpy as np
from src.spid import spid_v4

# 1. Define an invalid empirical correlation matrix (e.g., Higham 4x4)
G = np.array([
    [ 2.0, -1.0,  0.0,  0.0],
    [-1.0,  2.0, -1.0,  0.0],
    [ 0.0, -1.0,  2.0, -1.0],
    [ 0.0,  0.0, -1.0,  2.0]
])

# 2. Compute the Nearest Correlation Matrix using SPID v4
X_opt, iterations = spid_v4(G, tol=1e-5)

print(f"Converged in {iterations} iterations.")
print("Repaired Matrix:")
print(np.round(X_opt, 4))

```

## Reproducing the Paper Results

To reproduce the numerical experiments and ablation studies from the manuscript, run the benchmark script from your terminal:

```bash
python benchmarks/run_benchmarks.py

```

This script will run SPID v4 head-to-head against Higham's Baseline (MAP) and Anderson Acceleration ($m=4$). It evaluates:

1. **Classical Test Matrices** (e.g., Higham Ex 3.1)
2. **Missing Data Topologies** (e.g., 25% MCAR / NMAR missingness applied to financial return datasets)

**Expected Output:**
You will see SPID v4 consistently matching the optimal Frobenius distance of the MAP baseline while requiring ~52-64% fewer iterations, entirely avoiding the collinearity instability that occasionally plagues Anderson Acceleration on dense matrices.

## Citation

If you use SPID v4 or this codebase in your research, please cite the accompanying manuscript:

```bibtex
@article{ibrahim2026spid,
  title={Accelerated Computation of the Nearest Correlation Matrix via Spectral Picard-Ishikawa-Dykstra (SPID) Iterations},
  author={Ibrahim, Oyeyinka E.},
  journal={Submitted for Publication},
  year={2026},
  institution={Northern Illinois University}
}

```

## License

This project is licensed under the MIT License - see the LICENSE file for details.

```

```
