# Toy QSVT Poisson Demo

A compact notebook demo of using Quantum Signal Processing (QSP) and Quantum
Singular Value Transformation (QSVT) on a two-point finite-difference
discretization of the 1D Poisson equation.

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

The notebook `via_QSVT.ipynb` walks through the construction for this tiny
matrix:

1. Normalize the matrix as `B = A / alpha`.
2. Build a Hermitian signal unitary whose top-left block is `B`.
3. Set the inverse target by approximating `gamma / x` on the nonzero spectrum
   of `B`.
4. Choose a degree-3 polynomial that matches `gamma / x` on the two eigenvalues
   of `B`.
5. Build the matrix-level QSP/QSVT unitary for that polynomial.
6. Draw and simulate the corresponding Qiskit circuit.
7. Postselect the signal ancilla and compare the output with the classical
   solution.

The notebook also reviews scalar QSP, multi-qubit QSP, block encoding, and the
way QSVT applies polynomial transformations to singular values. Since this
example has only two eigenvalues, the polynomial is chosen by interpolation at
those eigenvalues. Scaling the same idea to larger Poisson matrices requires a
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
