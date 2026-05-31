# Inverse Conformal Risk Control

[![arXiv](https://img.shields.io/badge/arXiv-2510.07750-b31b1b.svg)](https://arxiv.org/abs/2510.07750)
[![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)]()
[![License](https://img.shields.io/badge/License-MIT-green.svg)]()

Inverse Conformal Risk Control (ICRC) is a distribution-free framework for constructing uncertainty sets for decision-focused optimization. Rather than starting from a prediction set and studying the downstream decision risk, ICRC works in the reverse direction: it calibrates uncertainty sets directly to satisfy user-specified constraints on optimization regret while controlling uncertainty set coverage.

This repository provides a reference implementation of ICRC for robust linear optimization under box uncertainty sets, together with a post-hoc model selection procedure based on CREME.

## Repository Structure

```text
.
├── model
│   ├── icrc.py          # Inverse Conformal Risk Control
│   └── creme.py         # CREME post-hoc model selection
│
├── optimization
│   ├── base.py          # Optimization interface
│   └── lp.py            # Robust linear programming example
│
├── demo.ipynb           # End-to-end demonstration
└── README.md
```


## Core Components

### InverseConformalRiskControl

The main class implements:

- calibration of uncertainty set radii
- empirical regret estimation
- empirical coverage estimation
- finite-sample upper confidence bounds
- feasible uncertainty set selection

Key outputs include:

- estimated regret
- estimated miscoverage
- confidence-adjusted upper bounds
- feasible values of $\lambda$


### CREME

CREME is used for post-hoc model selection.

Given a collection of candidate uncertainty radii:

$$
\Lambda = \{\lambda_1,\dots,\lambda_m\},
$$

CREME performs:

1. Candidate evaluation
2. Feasibility filtering
3. Post-hoc selection with finite-sample guarantees

This allows selecting the largest feasible uncertainty radius without invalidating statistical guarantees.

### Robust Linear Program

The repository includes a simple robust LP example.

Nominal objective:

$$
\min_z y^\top z
$$

subject to linear constraints

$$
Az \le b.
$$

Assuming box uncertainty

$$ \mathcal{U}_\lambda = \{ y : \|y-y_0\|_\infty \le \lambda \}, $$

the robust counterpart becomes

$$
\min_z
y_0^\top z
+
\lambda\|z\|_1.
$$

This reformulation is implemented using CVXPY.

## Installation

Create a Python environment and install the required packages:

```bash
pip install numpy scipy pandas cvxpy matplotlib tqdm
```

or

```bash
conda install numpy scipy pandas matplotlib
pip install cvxpy tqdm
```

## Quick Start

```python
from optimization.lp import LinearProgram
from model.icrc import InverseConformalRiskControl

# Define optimization problem
problem = LinearProgram(
    y_center=y_center,
    A=A,
    b=b
)

# Candidate uncertainty radii
lambdas = np.linspace(0, 5, 50)

# Initialize ICRC
icrc = InverseConformalRiskControl(
    optimization_problem=problem,
    y_cal=y_cal,
    lambda_list=lambdas
)

# Estimate regret and coverage
icrc.estimate()

# Select feasible radii
feasible = icrc.get_feasible_lambdas(
    alpha_regret=0.1,
    alpha_coverage=0.1,
    regret_budget=1.0,
    coverage_budget=0.1
)
```

## Example Workflow

1. Generate calibration data.
2. Define a robust optimization problem.
3. Specify candidate uncertainty radii.
4. Compute:
   - regret estimates
   - coverage estimates
   - confidence-adjusted bounds
5. Select feasible uncertainty sets.
6. Optionally apply CREME for post-hoc model selection.

See:

```text
demo.ipynb
```

for a complete example.

## Dependencies

- Python 3.9+
- NumPy
- SciPy
- Pandas
- CVXPY
- Matplotlib
- tqdm

## Notes

This repository currently serves as a research prototype illustrating the Inverse Conformal Risk Control framework and its application to robust optimization under box uncertainty.

Future extensions may include:

- nonlinear optimization problems
- mixed-integer optimization
- alternative uncertainty set geometries
- decision-aware conformal prediction
- distributionally robust optimization
- online and sequential calibration

## Citation

If you use this repository in academic work, please cite:

```bibtex
@misc{zhou2026calibratingdecisionrobustnessinverse,
      title={Calibrating Decision Robustness via Inverse Conformal Risk Control}, 
      author={Wenbin Zhou and Shixiang Zhu},
      year={2026},
      eprint={2510.07750},
      archivePrefix={arXiv},
      primaryClass={stat.ML},
      url={https://arxiv.org/abs/2510.07750}, 
}
```
