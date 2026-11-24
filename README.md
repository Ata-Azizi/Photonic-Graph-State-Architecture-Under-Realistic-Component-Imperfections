# Photonic Graph-State Architecture under Component Imperfections

Numerical study of ideal photonic graph states, probabilistic preparation, and the effects of local and correlated Pauli phase noise. The analysis combines small-system statevector and density-matrix calculations, stabilizer checks, exact error enumeration, Monte Carlo simulations, and graph-based scheduling. An extension within the **same notebook** examines nonidentical component survival and dephasing parameters.

All component parameters are illustrative. The work uses phenomenological preparation and noise models; it is not a calibrated quantum-dot hardware model or a simulation of a complete optical fusion network.

## Notebook

[`Photonic_Graph_State_Architecture.ipynb`](Photonic_Graph_State_Architecture.ipynb) contains two consecutive parts:

| Section | Analysis |
| --- | --- |
| **1–15 · Baseline model** | Graph-state construction and stabilizers, independent photon survival and heralded edge success, local and correlated phase faults, exact fidelity calculations, Monte Carlo checks, topology comparisons, and edge-operation scheduling |
| **16 · Heterogeneous-component extension** | Site-dependent photon survival and dephasing, density-matrix verification, a hypothetical graph-degree exposure model, and Monte Carlo cross-checks |

The extension follows the base-model results in the same notebook. Select the Python kernel and run the cells **from top to bottom**. The extension uses separate graph and parameter variables; its results are for a distinct component-noise scenario and should not be interpreted as including all the baseline noise mechanisms simultaneously.

## Mathematical model

For an undirected graph $G=(V,E)$ with $N=|V|$, the ideal graph state is

$$
|G\rangle = \left(\prod_{(i,j)\in E} CZ_{ij}\right)|+\rangle^{\otimes N}.
$$

The stabilizer generator associated with vertex $i$ is

$$
K_i = X_i\prod_{j\in\mathcal N(i)} Z_j.
$$

Exact statevector calculations and stabilizer checks establish reference cases for small graphs.

### Baseline: homogeneous components and correlated edge faults

The baseline assumes independent photon survival with probability $\eta$, independent heralded success for each idealized edge operation with probability $s$, independent local $Z_i$ errors with probability $p$, and edge-associated $Z_iZ_j$ faults with probability $q$. A preparation is accepted only when all photons and edge operations succeed, without retries:

$$
P_{\mathrm{accept}} = \eta^N s^{|E|}.
$$

Let $B$ be the vertex–edge incidence matrix over $\mathrm{GF}(2)$, and let $\boldsymbol a$ and $\boldsymbol b$ denote sampled local and edge fault patterns. The resulting $Z$-error pattern is

$$
\boldsymbol z = \boldsymbol a + B\boldsymbol b \pmod 2.
$$

The conditional overlap fidelity with the target graph state is

$$
F_{\mathrm{cond}} = \Pr(\boldsymbol z=\boldsymbol 0).
$$

This accounts for cancellations of Pauli faults. The probability-weighted target overlap per attempt is $Q=P_{\mathrm{accept}}F_{\mathrm{cond}}$. The model compares nine-qubit path, cycle, star, and $3\times3$ grid graphs using exact error-pattern enumeration, analytical acceptance probabilities, and Monte Carlo estimates. A separate scheduling calculation finds valid parallel sets of idealized edge operations; it does not establish hardware-optimal timings. Despite having identical vertex and edge counts, the path and star exhibit slightly different exact fidelities here because joint local and edge faults have topology-dependent cancellation patterns.

### Extension: heterogeneous local survival and phase noise

Uniform $\eta$ and $p$ can hide component-to-component variation. Section 16 uses $\eta_i$ and $p_i$, with independent events, to obtain

$$
P_{\mathrm{accept}} = \prod_i \eta_i, \qquad
F_{\mathrm{cond}} = \prod_i(1-p_i), \qquad
Q=P_{\mathrm{accept}}F_{\mathrm{cond}}.
$$

A direct density-matrix calculation checks the conditional fidelity. Monte Carlo sampling checks the analytical acceptance and probability-weighted overlap. For a controlled topology-sensitivity example, the extension assumes

$$
t_i=t_0+d_it_g,\qquad
p_i=\frac{1-e^{-t_i/T_2}}{2},
$$

where $d_i$ is vertex degree. This exposure law is a **hypothesis for sensitivity analysis**, not an experimentally derived quantum-dot spin-decoherence model. Under this assumption, a star may have a slightly higher conditional fidelity than a path even though its hub experiences more dephasing; this is a consequence of the nonlinear exposure law and should not be generalized to a shared circuit-time or idle-dephasing model. In this section, the base model's edge-heralding failures and correlated edge errors are deliberately excluded to isolate heterogeneous local parameters.

## Installation and execution

Use Python 3.10 or later. Clone or download the repository, then run from its root:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install numpy networkx matplotlib jupyterlab ipykernel
jupyter lab Photonic_Graph_State_Architecture.ipynb
```

On Windows, use `.venv\Scripts\activate` to activate the environment. In JupyterLab, select the environment's Python kernel and choose **Run All**. All required computations are included in the notebook; no external data file is needed.

For an optional independent Qiskit statevector check:

```bash
python -m pip install qiskit
```

The Qiskit cell skips the comparison if Qiskit is unavailable. The remaining calculations use NumPy, NetworkX, and Matplotlib. The optional Qiskit comparison is not necessary for reproducing the reported numerical tables.

## Representative numerical results

Input parameters are hypothetical and **not experimental benchmarks**.

For the nine-qubit graphs with $\eta=0.98$, $s=0.97$, $p=0.01$, and $q=0.02$:

| Graph | Edges | Analytical $P_{\mathrm{accept}}$ | Exact $F_{\mathrm{cond}}$ |
| --- | ---: | ---: | ---: |
| Path | 8 | 0.653444 | 0.777200 |
| Cycle | 9 | 0.633841 | 0.761658 |
| Star | 8 | 0.653444 | 0.777201 |
| $3\times3$ grid | 12 | 0.578490 | 0.716871 |

For the extension's four-qubit example, $\boldsymbol\eta=(0.96,0.93,0.91,0.97)$ and $\boldsymbol p=(0.01,0.025,0.035,0.015)$:

| Metric | Analytical value |
| --- | ---: |
| All-photon acceptance probability | 0.788075 |
| Conditional target fidelity | 0.917494 |
| Probability-weighted target overlap | 0.723054 |

The notebook includes plots, explicit random seeds, deterministic checks, and Monte Carlo comparisons with five-standard-error tolerances. The sample-size convergence study uses independent seeds for each run. The analytical and sampled quantities should agree within the stated statistical tolerance, not exactly for every realization.

## Interpretation and limitations

Graph topology influences edge count, possible cancellations among correlated phase faults, and the number of nonoverlapping operations available for parallel scheduling. These are comparisons **within the specified preparation model**, not rankings of different graphs executing equivalent quantum-computing tasks.

The baseline assumes ideal graph-state operations, independent loss and heralding, no retry procedure, and stochastic Pauli faults. The extension refines local component noise but does not combine every noise mechanism into a unified device model. The models do not simulate microscopic emitter dynamics, photon indistinguishability, explicit optical fusion measurements, detector dark counts, feed-forward, time-bin storage, loss-tolerant recovery, or logical error correction. In particular, neither conditional fidelity nor $Q$ is a fault-tolerance threshold or logical error rate.

Exact quantum states scale exponentially with qubit count, and exhaustive edge-fault enumeration scales as $2^{|E|}$. Numerical conclusions are restricted to the input assumptions, topology set, and model size studied here.
