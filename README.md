# Toy QSVT Poisson Solver

This repository contains a small notebook demo for solving a 1D Poisson finite
difference system with Quantum Singular Value Transformation (QSVT).

The model problem is

$$
-u''(x)=f(x), \quad x\in(0,1), \quad u(0)=u(1)=0.
$$

Using two interior grid points gives the linear system

$$
A u = f, \qquad
A =
\begin{bmatrix}
2 & -1 \\
-1 & 2
\end{bmatrix}.
$$

The notebook `via_QSVT.ipynb` walks through a tiny QSP/QSVT demo for this
matrix:

1. Normalize the matrix as `B = A / alpha`.
2. Build a Hermitian signal unitary whose top-left block is `B`.
3. Identify the inverse-function target `p(x) approx gamma / x`.
4. Choose a degree-3 polynomial that matches `gamma/x` on the two eigenvalues
   of `B`.
5. Build the matrix-level QSP/QSVT unitary for that polynomial.
6. Draw and simulate the corresponding Qiskit circuit.
7. Postselect the signal ancilla and compare the output with the classical
   solution.

The notebook also reviews scalar QSP, multi-qubit QSP, block encoding, and how
QSVT applies polynomial transformations to singular values. This two-eigenvalue
example uses a small matching polynomial; a larger Poisson solver would need a
general polynomial approximation and phase synthesis step.

## Run

```bash
pip install -r requirements.txt
jupyter notebook via_QSVT.ipynb
```

## Files

```text
README.md
requirements.txt
via_QSVT.ipynb
```

## References

- Childs, Liu, and Ostrander, "High-precision quantum algorithms for partial
  differential equations", arXiv:2002.07868.
- Shaviner, Chen, and Frankel, "Quantum Singular Value Transformation for
  Solving the Time-Dependent Maxwell's Equations", arXiv:2507.09686.
