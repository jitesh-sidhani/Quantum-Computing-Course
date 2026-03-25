# Simon's Algorithm — Quantum Computing Assignment

> A complete Qiskit implementation of Simon's Algorithm demonstrating quantum exponential speedup over classical approaches.

---

## Overview

**Simon's Algorithm** solves the following problem exponentially faster than any classical algorithm:

Given a black-box function $f: \{0,1\}^n \to \{0,1\}^n$ with the promise that

$$f(x) = f(y) \iff x = y \oplus s$$

find the hidden secret string $s \in \{0,1\}^n$.

| Complexity | Classical | Simon's (Quantum) |
|---|---|---|
| Query complexity | $O(2^{n/2})$ | $O(n)$ |

---

## Chosen Secret Strings

| Part | Secret String | Qubits |
|------|--------------|--------|
| Part A (reference) | `s = 011` | 3 |
| Part B (main) | `s = 10110` | 5 |

---

## Notebook Structure

```
simons_algorithm.ipynb
├── 1. Environment Setup
│   └── Package installation and library imports
├── 2. Part A — Reference Implementation (s = 011)
│   ├── Simon oracle for 3-bit secret
│   ├── Full Simon circuit (H → Oracle → H → Measure)
│   ├── Simulation (2000 shots)
│   ├── Circuit diagram and histogram
│   └── Explanation of each component
├── 3. Part B — 5-bit Secret String (s = 10110)
│   ├── General n-bit Simon oracle
│   ├── Full 5-qubit Simon circuit
│   ├── Simulation (2000 shots)
│   └── Extraction of measurement outcomes
├── 4. Part C — Classical Post-Processing
│   ├── Matrix U construction
│   ├── GF(2) Gaussian elimination
│   ├── Null-space computation
│   └── Secret string recovery
└── 5. Results and Verification
    └── Final summary and correctness check
```

---

## Recovered Secret String

After running Simon's Algorithm for `s = 10110` and solving the resulting linear system over GF(2):

```
Recovered s = 10110  ✓  (matches original)
```

All measured bitstrings $u$ satisfy $u \cdot s = 0 \pmod{2}$.

---

## How to Run

### Option 1 — Local Jupyter Notebook

1. **Clone the repository**
   ```bash
   git clone https://github.com/<your-username>/<repo-name>.git
   cd <repo-name>
   ```

2. **Create a virtual environment (recommended)**
   ```bash
   python -m venv venv
   source venv/bin/activate        # macOS / Linux
   venv\Scripts\activate           # Windows
   ```

3. **Install dependencies**
   ```bash
   pip install qiskit qiskit-aer numpy matplotlib pylatexenc jupyter
   ```

4. **Launch Jupyter**
   ```bash
   jupyter notebook simons_algorithm.ipynb
   ```

5. **Run all cells**  
   Use `Kernel → Restart & Run All` to execute the full notebook from scratch.

---

### Option 2 — Google Colab

1. Open [Google Colab](https://colab.research.google.com/)
2. Upload `simons_algorithm.ipynb`
3. The first cell installs all required packages automatically
4. Run all cells with `Runtime → Run all`

---

## Requirements

| Package | Version |
|---------|---------|
| Python | ≥ 3.9 |
| qiskit | ≥ 1.0 |
| qiskit-aer | ≥ 0.14 |
| numpy | ≥ 1.24 |
| matplotlib | ≥ 3.7 |
| pylatexenc | ≥ 2.10 |

---

## Algorithm Walkthrough

### Quantum Circuit

```
|0⟩ ─── H ─────────────── H ─── Measure
|0⟩ ─── H ─────────────── H ─── Measure
|0⟩ ─── H ──┤   Oracle  ├─ H ─── Measure
|0⟩ ──────── │           │
|0⟩ ──────── │           │
```

### Why It Works

1. **First H layer**: Creates uniform superposition — queries all $2^n$ inputs at once
2. **Oracle**: Entangles registers, encoding $f(x) = f(x \oplus s)$
3. **Second H layer**: Quantum interference keeps only $u$ with $u \cdot s = 0 \pmod 2$
4. **Measurement**: Samples from the valid $u$ values
5. **Classical solver**: Uses GF(2) linear algebra to recover $s$

---

## File Structure

```
.
├── simons_algorithm.ipynb   # Main Jupyter Notebook
└── README.md                # This file
```

---

## Circuit Visualisation

Qiskit's `'mpl'` circuit drawer requires **`pylatexenc`** to render gate labels. Without it, every `qc.draw('mpl', ...)` call raises:

```
MissingOptionalLibraryError: "The 'pylatexenc' library is required to use 'MatplotlibDrawer'..."
```

This notebook handles this in two ways:

1. **`pylatexenc` is included in the install cell** — so it is installed automatically when the first cell runs.
2. **A `draw_circuit()` helper wraps every circuit draw call** — if `mpl` rendering still fails for any reason (e.g. a restricted environment), the helper silently falls back to ASCII text mode and the notebook continues running without error.

```python
def draw_circuit(qc, fold=25, style='iqp'):
    try:
        fig = qc.draw('mpl', style=style, fold=fold)
        display(fig)
        plt.show()
    except Exception as e:
        print(f"[mpl draw error: {e}]")
        print(qc.draw('text', fold=fold))   # graceful fallback
```

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| `MissingOptionalLibraryError: pylatexenc` | Run `!pip install pylatexenc` then restart the kernel |
| `ModuleNotFoundError: qiskit_aer` | Run `!pip install qiskit-aer` then restart the kernel |
| Circuit diagrams show as text instead of graphics | Ensure `pylatexenc` is installed; re-run the setup cell |
| Recovered `s` doesn't match original | Re-run Part B — with probabilistic simulation, occasionally too few linearly independent equations are sampled; running again resolves it |

---

## References

- Simon, D.R. (1997). *On the Power of Quantum Computation*. SIAM Journal on Computing.
- [Qiskit Documentation](https://docs.quantum.ibm.com/)
- [IBM Quantum Learning — Simon's Algorithm](https://learning.quantum.ibm.com/)
