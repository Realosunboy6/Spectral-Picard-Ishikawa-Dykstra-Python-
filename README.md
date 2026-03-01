# SPID: Spectral Picard-Ishikawa-Dykstra Iterations

[![Language](https://img.shields.io/badge/Language-Julia_1.11-blue.svg)](https://julialang.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This repository contains the official Julia implementation of the **SPID v4** algorithm, as presented in the paper:
> **Accelerated Computation of the Nearest Correlation Matrix via Spectral Picard-Ishikawa-Dykstra (SPID) Iterations** > *Oyeyinka E. Ibrahim (2026)*

## Overview
The Nearest Correlation Matrix (NCM) problem involves projecting a symmetric matrix onto the intersection of the positive semidefinite cone and the affine subspace of unit-diagonal matrices. This problem arises routinely in quantitative finance when empirical estimates (e.g., from asynchronous or missing data) fail to be valid correlation matrices.

**SPID v4** is a novel, memory-efficient primal solver that:
1. Wraps the classical Dykstra cycle in an Ishikawa-style convex combination.
2. Applies a safeguarded Alternating Barzilai-Borwein (ABB) spectral step to aggressively accelerate convergence.
3. Maintains a strict $\mathcal{O}(n^2)$ memory footprint, requiring only two additional $n \times n$ matrices beyond the base Dykstra state (vastly lighter than Anderson Acceleration).
4. Strictly preserves exact unit-diagonal feasibility ($\text{diag}(X) = 1$).

## Repository Structure
* `src/spid.jl`: Contains the core implementation of the `spid_v4` algorithm, alongside the baseline Modified Alternating Projections (MAP) and Anderson Acceleration (AA) methods.
* `benchmarks/run_benchmarks.jl`: The reproduction script that generates the exact performance tables presented in the manuscript (including the Higham $4 \times 4$, Toeplitz $6 \times 6$, and simulated financial missing-data matrices).
* `README.md`: Project documentation.

## Requirements
* **Julia** 1.11 or higher
* Standard Library Modules: `LinearAlgebra`, `Random`, `Printf`

## Quick Start
You can easily use SPID v4 in your own Julia projects to repair invalid correlation matrices.

```julia
include("src/spid.jl")

# 1. Define an invalid empirical correlation matrix (e.g., Higham 4x4)
G = [ 2.0 -1.0  0.0  0.0;
     -1.0  2.0 -1.0  0.0;
      0.0 -1.0  2.0 -1.0;
      0.0  0.0 -1.0  2.0]

# 2. Compute the Nearest Correlation Matrix using SPID v4
X_opt, iterations, runtime = spid_v4(G, tol=1e-5)

println("Converged in $iterations iterations.")
display(X_opt)
