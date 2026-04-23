# ADL

MATLAB implementation of **ADL (Accelerated Dictionary Learning)** for sparse signal representation.

This repository is based on the paper:

**Fateme Ghayem, Mostafa Sadeghi, Massoud Babaie-Zadeh, Christian Jutten**  
**“Accelerated Dictionary Learning for Sparse Signal Representation”**  
LVA/ICA 2017

Paper: [ADL_LVA_ICA_2017.pdf](https://ghayem.github.io/files/ADL_LVA_ICA_2017.pdf)

---

## Overview

Dictionary learning aims to learn a dictionary `D` and sparse coefficients `X` from training signals `Y` so that the data can be well approximated by

`Y ≈ D X`.

In the formulation used in the paper, the learning problem is

**minimize over `D` and `X`:**

`0.5 * ||Y - D X||_F^2 + lambda * ||X||_1`

subject to unit-norm columns in `D`.

In other words, the method seeks:
- a dictionary `D` whose columns are normalized atoms,
- a sparse coefficient matrix `X`,
- and a small reconstruction error.

The main idea of ADL is to solve dictionary learning by alternating between:

1. a **sparse representation step** for updating `X`,
2. a **dictionary update step** for updating `D`,

while using **accelerated proximal gradient-style iterations** with extrapolation/inertial terms in both steps. The paper reports faster convergence and better recovery performance than several baseline methods on synthetic dictionary recovery experiments. 

---

## Repository contents

- `ADL.m` — main accelerated dictionary learning solver
- `my_acc_sparse.m` — accelerated sparse coding update used inside ADL
- `my_softh.m` — soft-thresholding helper
- `MBHPA.m` — baseline or related proximal dictionary learning routine
- `I_findDistanseBetweenDictionaries.m` — dictionary comparison utility
- `gererateSyntheticDictionaryAndData.m` — synthetic data generation script
- `test_ADL.m` — example script for reproducing synthetic dictionary recovery experiments

---

## Method summary

The paper considers the dictionary learning problem

`min_{D in D, X} 0.5 * ||Y - D X||_F^2 + lambda * ||X||_1`

where `D` is the set of dictionaries whose columns all have unit Euclidean norm.

### 1) Sparse representation step

Given the current dictionary, ADL updates the coefficient matrix `X` using a proximal gradient step on

`0.5 * ||Y - D X||_F^2 + lambda * ||X||_1`.

Because the penalty is `l1`, the proximal operator becomes **soft-thresholding**:

`Soft_lambda(x) = sign(x) * max(|x| - lambda, 0)`.

The method then accelerates this update using an extrapolation term with weight `w_x`.

### 2) Dictionary update step

Given the current coefficients, ADL updates the dictionary `D` using another proximal gradient-type iteration, followed by projection onto the set of unit-norm columns. This step is also accelerated using an inertial/extrapolation term with weight `w_d`. 

### 3) Alternating optimization

These two steps are alternated for a fixed number of outer iterations. The full ADL procedure is summarized in Algorithm 1 of the paper. 

