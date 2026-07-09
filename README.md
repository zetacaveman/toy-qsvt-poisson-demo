# Toy Quantum Linear-System Solver for the 1D Poisson Equation

This project is a small, pedagogical demonstration of the
finite-difference-to-quantum-linear-system pipeline used in the Poisson
equation setting of Childs, Liu, and Ostrander, "High-precision quantum
algorithms for partial differential equations" (arXiv:2002.07868).

The paper studies quantum algorithms for elliptic PDEs, especially Poisson's
equation. This repository currently keeps a notebook-only version of the
smallest possible example of the core mechanism:

```text
PDE -> finite-difference matrix A -> linear system A u = f
    -> QSVT/QSP polynomial inverse -> |u> proportional to A^{-1}|f>
```

The model problem is the 1D Poisson equation

$$-u''(x)=f(x), \quad x\in[0,1], \quad u(0)=u(1)=0.$$

On a two-point interior grid, the finite-difference method turns the PDE into a
linear system

$$A u = f,$$

with

$$A = \begin{bmatrix}2 & -1 \\ -1 & 2\end{bmatrix}$$

up to an irrelevant global scale factor. Because the quantum output is a normalized state, the overall scale is ignored.

The notebook implements a tiny hand-written QSVT/QSP construction that prepares
a state proportional to the solution of the linear system,

$$|u\rangle \propto A^{-1}|f\rangle.$$

This is a pedagogical toy version, not the full adaptive-order high-precision
algorithm from the paper. The full paper uses adaptive-order finite
differences, QFT structure, and high-precision LCU-based QLSA. This project
only demonstrates the central mechanism: encode the right-hand side as a
state, apply a bounded polynomial inverse through a small QSVT/QSP sequence,
and postselect to obtain the normalized solution state.

## QSVT Direction

The repository now centers on `via_QSVT.ipynb`. It does not call a QSVT
library. The notebook manually builds a Hermitian signal unitary whose top-left
block is `B = A / alpha`, applies a fixed degree-3 QSP phase sequence, and
postselects the signal ancilla. The chosen polynomial is bounded on `[-1, 1]`
and acts as a scaled inverse on the two eigenvalues of the normalized Poisson
matrix.

This is still a toy implementation. It uses a fixed low-degree phase sequence
for this specific matrix instead of a general phase-angle synthesis algorithm.

QSVT is an important next step for this demo. A full QSVT-based Poisson solver
would replace the explicit eigenvalue-inversion rotation with a polynomial
transformation of a block-encoding of the discretized operator. The intended
roadmap is:

1. Build a block-encoding of the normalized finite-difference Poisson matrix.
2. Approximate the inverse function, or a regularized inverse, by a bounded
   polynomial on the relevant singular-value interval.
3. Synthesize QSVT phase angles for that polynomial.
4. Apply the QSVT circuit to a prepared right-hand-side state `|f>`.
5. Compare the postselected or measured output against the classical normalized
   solution. A separate HHL baseline can be restored later when the project
   grows into a more general solver.

A useful reference for this direction is Shaviner, Chen, and Frankel,
"Quantum Singular Value Transformation for Solving the Time-Dependent Maxwell's
Equations" (arXiv:2507.09686). That work applies QSVT to a linear-system form
of Maxwell's equations using a block-encoding of the system matrix and a
classically optimized polynomial approximation to `1/x`. Although the PDE and
operator structure differ from this Poisson toy problem, it is a practical
example of the QSVT workflow this project can learn from:

- formulate the PDE discretization as a linear system,
- block-encode the relevant matrix,
- use a polynomial inverse implemented by QSVT,
- validate the normalized quantum solution against a classical solver,
- keep in mind that full-state access in simulation is much easier than
  measurement-efficient extraction on hardware.

Reference:

- Gal G. Shaviner, Ziv Chen, Steven H. Frankel, "Quantum Singular Value
  Transformation for Solving the Time-Dependent Maxwell's Equations",
  arXiv:2507.09686, https://arxiv.org/abs/2507.09686

## Project Layout

```text
README.md
requirements.txt
via_QSVT.ipynb
```

## Run

```bash
pip install -r requirements.txt
jupyter notebook via_QSVT.ipynb
```

The notebook prints the matrix, right-hand side, signal-unitary checks, QSP
phase sequence, inverse-response polynomial, postselected solution state, and
fidelity against the classical normalized solution.

For the included example

```python
f = np.array([1.0, 0.3])
```

the quantum postselected state should match the classical normalized solution
with fidelity close to `1.0`.
