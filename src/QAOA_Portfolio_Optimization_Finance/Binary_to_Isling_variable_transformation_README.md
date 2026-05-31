# Binary to Ising Variable Transformation in QAOA

## Overview

The **Quantum Approximate Optimization Algorithm (QAOA)** is a variational quantum algorithm designed to solve combinatorial optimization problems. Most such problems are naturally expressed using **binary variables** (`0` or `1`), but the quantum hardware underlying QAOA operates with **Ising spin variables** (`+1` or `-1`). This document explains the transformation between the two representations and how it shapes the QAOA problem Hamiltonian.

---

## 1. The Two Variable Spaces

### Binary Variables (Classical)

Binary variables are the native language of combinatorial optimization:

$$x_i \in \{0, 1\}$$

They appear in problems like Max-Cut, Traveling Salesman, Portfolio Optimization, and Satisfiability.

### Ising Spin Variables (Quantum)

Ising variables map naturally onto qubit states:

$$s_i \in \{-1, +1\}$$

- Qubit state $|0\rangle$ corresponds to $s_i = +1$
- Qubit state $|1\rangle$ corresponds to $s_i = -1$

These are the eigenvalues of the **Pauli-Z operator**: $\hat{Z}|0\rangle = +1|0\rangle$ and $\hat{Z}|1\rangle = -1|1\rangle$.

---

## 2. The Transformation

The bijective mapping between the two spaces is:

$$\boxed{s_i = 1 - 2x_i}$$

or equivalently:

$$\boxed{x_i = \frac{1 - s_i}{2}}$$

### Truth Table

| Binary $x_i$ | Ising $s_i$ | Qubit State |
|:---:|:---:|:---:|
| 0 | +1 | $\|0\rangle$ |
| 1 | -1 | $\|1\rangle$ |

---

## 3. Transforming the Objective Function

### Step-by-Step Example: Max-Cut

The **Max-Cut** problem on a graph $G = (V, E)$ seeks to partition vertices into two sets to maximize the number of edges between them.

**Binary formulation** — edge $(i,j)$ is cut if $x_i \neq x_j$:

$$C = \sum_{(i,j) \in E} (x_i + x_j - 2x_i x_j)$$

**Substituting** $x_i = \frac{1 - s_i}{2}$:

$$x_i + x_j - 2x_i x_j = \frac{1-s_i}{2} + \frac{1-s_j}{2} - 2 \cdot \frac{1-s_i}{2} \cdot \frac{1-s_j}{2}$$

$$= 1 - \frac{s_i + s_j}{2} - \frac{(1-s_i)(1-s_j)}{2}$$

$$= 1 - \frac{s_i + s_j}{2} - \frac{1 - s_i - s_j + s_i s_j}{2}$$

$$= \frac{1 - s_i s_j}{2}$$

**Ising formulation:**

$$C = \frac{1}{2} \sum_{(i,j) \in E} (1 - s_i s_j)$$

Dropping the constant, the **cost Hamiltonian** becomes:

$$H_C = -\frac{1}{2} \sum_{(i,j) \in E} \hat{Z}_i \hat{Z}_j$$

---

## 4. General Transformation Rules

Any binary polynomial can be converted using these substitution rules:

| Binary Expression | Ising Equivalent |
|---|---|
| $x_i$ | $\dfrac{1 - s_i}{2}$ |
| $x_i x_j$ | $\dfrac{1 - s_i - s_j + s_i s_j}{4}$ |
| $x_i(1 - x_j)$ | $\dfrac{1 - s_i + s_j - s_i s_j}{4}$ |
| $x_i \oplus x_j$ (XOR) | $\dfrac{1 - s_i s_j}{2}$ |
| $(x_i - x_j)^2$ | $\dfrac{1 - s_i s_j}{2}$ |

### Key Identities

Because $s_i \in \{-1, +1\}$, the following simplifications hold:

- $s_i^2 = 1$ (spins are idempotent)
- $s_i^{2k} = 1$ for any integer $k$
- Higher-order binary terms collapse: $x_i^2 = x_i$, so $s_i^2 = 1$ eliminates squared terms

---

## 5. From Ising Model to Quantum Hamiltonian

Once the objective is in Ising form, each spin variable $s_i$ is promoted to the **Pauli-Z operator** $\hat{Z}_i$ acting on qubit $i$:

$$s_i \longrightarrow \hat{Z}_i, \qquad s_i s_j \longrightarrow \hat{Z}_i \hat{Z}_j$$

The general **QAOA cost Hamiltonian** takes the form:

$$H_C = \sum_{i} h_i \hat{Z}_i + \sum_{i < j} J_{ij} \hat{Z}_i \hat{Z}_j$$

where:
- $h_i$ are **local field** coefficients (linear terms)
- $J_{ij}$ are **coupling** coefficients (quadratic terms)

This is a **diagonal Hamiltonian** in the computational basis, which means it can be efficiently implemented as a quantum circuit.

---

## 6. The QAOA Circuit Structure

The full QAOA ansatz alternates between the **cost unitary** and the **mixer unitary** for $p$ layers:

$$|\psi(\vec{\gamma}, \vec{\beta})\rangle = \prod_{k=1}^{p} e^{-i\beta_k H_M} e^{-i\gamma_k H_C} |+\rangle^{\otimes n}$$

### Cost Unitary (from Ising Hamiltonian)

For each edge $(i,j)$ with weight $J_{ij}$:

$$e^{-i\gamma J_{ij} \hat{Z}_i \hat{Z}_j} \quad \Rightarrow \quad \text{CNOT} \cdot R_z(2\gamma J_{ij}) \cdot \text{CNOT}$$

For each local field $h_i$:

$$e^{-i\gamma h_i \hat{Z}_i} \quad \Rightarrow \quad R_z(2\gamma h_i)$$

### Mixer Unitary

$$H_M = \sum_i \hat{X}_i, \qquad e^{-i\beta H_M} = \prod_i R_x(2\beta)$$

---

## 7. Complete Worked Example: 3-Variable QUBO

Consider a Quadratic Unconstrained Binary Optimization (QUBO) problem:

$$\min_{x \in \{0,1\}^3} \quad 2x_1 + 3x_2 - x_3 - 4x_1 x_2 + 2x_1 x_3$$

**Step 1: Substitute** $x_i = \frac{1 - s_i}{2}$:

$$2 \cdot \frac{1-s_1}{2} + 3 \cdot \frac{1-s_2}{2} - \frac{1-s_3}{2} - 4 \cdot \frac{(1-s_1)(1-s_2)}{4} + 2 \cdot \frac{(1-s_1)(1-s_3)}{4}$$

**Step 2: Expand and collect terms:**

$$= (1 - \tfrac{s_1}{2}) + (\tfrac{3}{2} - \tfrac{3s_2}{2}) + (-\tfrac{1}{2} + \tfrac{s_3}{2}) - (1 - \tfrac{s_1}{2} - \tfrac{s_2}{2} + \tfrac{s_1 s_2}{4} \cdot 4) + (\tfrac{1}{2} - \tfrac{s_1}{4} - \tfrac{s_3}{4} + \tfrac{s_1 s_3}{2})$$

**Step 3: Collect Ising coefficients** (constant terms can be dropped for optimization):

| Term | Coefficient |
|---|---|
| Constant | absorbed |
| $s_1$ | $-\frac{1}{4}$ |
| $s_2$ | $+\frac{1}{2}$ |
| $s_3$ | $+\frac{1}{4}$ |
| $s_1 s_2$ | $-1$ |
| $s_1 s_3$ | $+\frac{1}{2}$ |

**Step 4: Write the cost Hamiltonian:**

$$H_C = -\tfrac{1}{4}\hat{Z}_1 + \tfrac{1}{2}\hat{Z}_2 + \tfrac{1}{4}\hat{Z}_3 - \hat{Z}_1\hat{Z}_2 + \tfrac{1}{2}\hat{Z}_1\hat{Z}_3$$


## 8. Summary

```
Binary variable (classical)        Ising variable (quantum)
─────────────────────────────      ──────────────────────────────
x_i ∈ {0, 1}                  →   s_i ∈ {-1, +1}
                                   
x_i = (1 - s_i) / 2               s_i = 1 - 2·x_i

Objective f(x)                 →   Ising Hamiltonian H_C(s)

s_i                            →   Pauli-Z operator Ẑ_i

s_i·s_j                        →   Ẑ_i ⊗ Ẑ_j (ZZ interaction)

Diagonal in computational basis:   Implemented as Rz and CNOT gates
```

The transformation is exact and lossless — every solution of the original binary problem corresponds bijectively to a ground state of the Ising Hamiltonian, which QAOA approximates by variationally optimizing the angles $(\vec{\gamma}, \vec{\beta})$.

---

## References

- Farhi, E., Goldstone, J., & Gutmann, S. (2014). *A Quantum Approximate Optimization Algorithm*. [arXiv:1411.4028](https://arxiv.org/abs/1411.4028)
- Lucas, A. (2014). *Ising formulations of many NP problems*. Frontiers in Physics, 2, 5.
- Glover, F., Kochenberger, G., & Du, Y. (2019). *Quantum Bridge Analytics I: a tutorial on formulating and using QUBO models*. 4OR, 17(4), 335–371. [arXiv:1811.11538](https://arxiv.org/abs/1811.11538)
