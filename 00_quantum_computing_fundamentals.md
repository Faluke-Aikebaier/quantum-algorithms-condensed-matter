# Quantum Computing Fundamentals

A self-contained introduction covering the physical concepts, circuit model, and key vocabulary needed to follow the notebooks in this repository. No prior quantum mechanics knowledge is assumed — only linear algebra at the level of vectors and matrices.

---

## 1. What Is a Qubit?

A classical bit is either 0 or 1. A **qubit** (quantum bit) can be in a **superposition** of both:

$$|\psi\rangle = \alpha|0\rangle + \beta|1\rangle$$

where $\alpha$ and $\beta$ are complex numbers called **amplitudes**, satisfying $|\alpha|^2 + |\beta|^2 = 1$. The probabilities of measuring 0 or 1 are $|\alpha|^2$ and $|\beta|^2$ respectively.

**The key point:** before measurement, the qubit is genuinely in both states at once — this is not ignorance about a hidden value, but a fundamental feature of quantum mechanics. Measurement irreversibly collapses the superposition to a definite outcome.

### The Bloch sphere

A single qubit state can be visualised as a point on the surface of a unit sphere (the **Bloch sphere**). The north pole is $|0\rangle$, the south pole is $|1\rangle$, and all other points are superpositions. Quantum gates rotate this sphere.

### Physical implementations

Qubits are realised in many different physical systems:

| Platform | Qubit | Coherence time | Gate time | Example hardware |
|----------|-------|---------------|-----------|-----------------|
| Superconducting | Josephson junction | ~100μs – 1ms | ~10–100 ns | AaltoQ20 (IQM), IBM, Google |
| Trapped ion | Electronic state of ion | ms – seconds | ~1–10 μs | IonQ, Quantinuum |
| Photonic | Photon polarisation | Very long | ~ps | PsiQuantum |
| Spin | Electron/nuclear spin | μs – ms | ~ns | Intel, Silicon Quantum Computing |

The **coherence time** is how long a qubit maintains its quantum state before environmental noise destroys it. The **gate time** is how long each operation takes. The ratio gives the **circuit depth budget** — how many operations you can perform reliably.

---

## 2. Quantum Gates

Quantum gates are the operations that manipulate qubits. They are **unitary matrices** — reversible linear transformations that preserve the normalisation $|\alpha|^2 + |\beta|^2 = 1$.

### Single-qubit gates

**Hadamard gate (H)** — creates superposition:
$$H = \frac{1}{\sqrt{2}}\begin{pmatrix}1 & 1\\1 & -1\end{pmatrix}, \qquad H|0\rangle = \frac{|0\rangle+|1\rangle}{\sqrt{2}}, \quad H|1\rangle = \frac{|0\rangle-|1\rangle}{\sqrt{2}}$$

**Pauli gates (X, Y, Z)** — bit flip, combined flip, phase flip:
$$X = \begin{pmatrix}0&1\\1&0\end{pmatrix}, \quad Y = \begin{pmatrix}0&-i\\i&0\end{pmatrix}, \quad Z = \begin{pmatrix}1&0\\0&-1\end{pmatrix}$$

**Rotation gates** — rotate around X, Y, or Z axis of the Bloch sphere by angle $\theta$:
$$R_y(\theta) = \begin{pmatrix}\cos(\theta/2) & -\sin(\theta/2)\\\sin(\theta/2) & \cos(\theta/2)\end{pmatrix}$$

**T gate** — $\pi/8$ phase rotation, essential for universal computation:
$$T = \begin{pmatrix}1&0\\0&e^{i\pi/4}\end{pmatrix}$$

### Two-qubit gates

**CNOT (Controlled-NOT)** — flips the target qubit if and only if the control qubit is $|1\rangle$. This gate creates **entanglement** — the essential resource that makes quantum computers more powerful than classical ones:
$$\text{CNOT}|00\rangle = |00\rangle, \quad \text{CNOT}|01\rangle = |01\rangle, \quad \text{CNOT}|10\rangle = |11\rangle, \quad \text{CNOT}|11\rangle = |10\rangle$$

**CZ (Controlled-Z)** — applies a Z gate to the target if the control is $|1\rangle$. Commonly the native gate on superconducting hardware (AaltoQ20 uses CZ).

**RZZ** — parametric two-qubit gate: $e^{-i\theta Z\otimes Z/2}$. Used directly in QAOA circuits to encode problem Hamiltonians.

### Universality

The set $\{H, T, \text{CNOT}\}$ is **universal** — any unitary operation on any number of qubits can be approximated arbitrarily well using only these three gates. This is the quantum analog of NAND universality in classical computing.

---

## 3. Quantum Circuits

A quantum circuit is a sequence of gates applied to qubits, read left to right. The circuit diagram is the standard notation:

```
q0: ──H──●────── M
         |
q1: ─────X────── M
```

This creates a Bell state: H on q0 creates superposition, CNOT entangles q0 and q1.

### Circuit depth and width

- **Width**: number of qubits
- **Depth**: length of the longest path from input to output (roughly the number of sequential operations)
- **Size**: total number of gates

The **coherence budget** constrains depth: `circuit_depth × gate_time < coherence_time`. For AaltoQ20 with 1ms coherence and ~100ns two-qubit gates, the budget is roughly 5,000–10,000 two-qubit gates.

### Measurement

Measurement collapses a qubit to $|0\rangle$ or $|1\rangle$, with probabilities $|\alpha|^2$ and $|\beta|^2$. A quantum circuit is typically run thousands of times (**shots**) to estimate these probabilities from the frequency of outcomes.

---

## 4. Superposition, Entanglement, and Interference

These three phenomena are the engine of quantum computing:

**Superposition** allows a quantum computer to represent $2^n$ states simultaneously with $n$ qubits. After applying $n$ Hadamard gates, the system is in the equal superposition of all $2^n$ computational basis states — every possible input evaluated at once. However, measurement collapses this to a single outcome — you cannot simply read out all $2^n$ values.

**Entanglement** is a correlation between qubits that has no classical analog. In the Bell state $(|00\rangle + |11\rangle)/\sqrt{2}$, measuring the first qubit instantly determines the second, regardless of their physical separation. Entanglement enables quantum algorithms to process information in ways classically impossible — it is the resource that underlies quantum teleportation, quantum error correction, and quantum advantage in algorithms.

**Interference** is how quantum algorithms extract useful answers from superposition. An algorithm is designed so that amplitudes for wrong answers cancel (destructive interference) and amplitudes for the correct answer reinforce (constructive interference). This is why quantum algorithms can find a correct answer with high probability despite the exponential superposition collapsing to a single measurement outcome.

---

## 5. The NISQ Era

Today's quantum computers are **NISQ** devices (Noisy Intermediate-Scale Quantum). The term, coined by John Preskill in 2018, describes machines with:

- **20–1,000 qubits** — enough to represent states classical computers cannot store, but not enough for large-scale fault-tolerant computation
- **No error correction** — qubits are noisy and operations have ~0.1–1% error rates
- **Limited coherence** — circuits must complete within microseconds to milliseconds

### AaltoQ20

The primary hardware context for this repository is **AaltoQ20** — a 20-qubit superconducting processor developed by IQM and operated at Aalto University, Finland. Its characteristics:

| Property | Value |
|----------|-------|
| Qubit count | 20 |
| Coherence time ($T_2$) | ~1 ms |
| Native 2-qubit gate | CZ |
| 2-qubit gate time | ~100 ns |
| 2-qubit gate fidelity | ~99–99.5% |
| Circuit depth budget | ~5,000–10,000 CZ gates |

AaltoQ20 has among the longest coherence times of any superconducting processor in the world, making it well-suited for deeper circuits than IBM or Google hardware despite having fewer qubits.

### The hardware timeline

| Era | Approx. dates | Characteristics | What becomes possible |
|-----|--------------|-----------------|----------------------|
| NISQ | 2019–2027 | 20–1,000 noisy qubits | Research demonstrations, small optimisation |
| Early fault-tolerant | 2027–2032 | ~1,000 logical qubits | First classically hard chemistry, cryptography |
| Full fault-tolerant | 2032+ | 10,000+ logical qubits | Shor's algorithm, large-scale quantum simulation |

---

## 6. Quantum Advantage — When Does It Appear?

**Quantum advantage** means the quantum computer solves a problem faster than the best classical algorithm. It is not automatic — it requires careful alignment between the problem structure and the quantum algorithm's strengths.

### Proven advantages

- **Grover's search**: $O(\sqrt{N})$ vs classical $O(N)$ for unstructured search — quadratic speedup, proven optimal
- **Shor's factoring**: $O((\log N)^3)$ vs classical sub-exponential — exponential speedup for RSA-relevant problem sizes
- **Quantum simulation**: exponential speedup for simulating quantum systems (Feynman's original motivation)

### Conditional advantages (active research)

- **VQE**: variational quantum eigensolver for chemistry — advantage only for strongly-correlated systems beyond classical reach (~50+ qubits)
- **QAOA**: combinatorial optimisation — no proven advantage over best classical heuristics yet
- **Grover-based optimisation**: advantage for multi-modal landscapes where classical methods need exponential restarts

### The honest picture

Classical computers are extremely fast, and clever classical algorithms (DMRG, coupled cluster, Goemans-Williamson) have raised the bar enormously. Quantum advantage must beat *these* — not brute force. For most problems that matter today, classical methods still win. The NISQ era is primarily about building the hardware, software, and algorithmic expertise that will translate into practical advantage when fault-tolerant machines arrive.

---

## 7. Key Vocabulary

| Term | Meaning |
|------|---------|
| **Qubit** | Quantum bit — two-level quantum system |
| **Superposition** | Linear combination of basis states |
| **Entanglement** | Non-classical correlations between qubits |
| **Interference** | Cancellation or reinforcement of amplitudes |
| **Gate fidelity** | Probability that a gate performs correctly (1 - error rate) |
| **Coherence time** | How long a qubit maintains its quantum state |
| **Circuit depth** | Number of sequential gate layers |
| **Shot** | Single execution and measurement of a circuit |
| **Oracle** | Black-box quantum circuit encoding a function |
| **Ansatz** | Parameterised trial circuit (used in VQE, QAOA) |
| **Hamiltonian** | Operator encoding the energy of a system |
| **Eigenvalue / eigenvector** | Solution to $H|\psi\rangle = E|\psi\rangle$ |
| **NISQ** | Noisy Intermediate-Scale Quantum — current hardware era |
| **Logical qubit** | Error-corrected qubit built from many physical qubits |
| **Quantum volume** | Benchmarking metric combining qubit count, fidelity, connectivity |
| **QTCI** | Quantics Tensor Cross Interpolation — efficient tensor compression |
| **QTT** | Quantics Tensor Train — compressed representation of a function |
| **FFLO** | Fulde-Ferrell-Larkin-Ovchinnikov — finite-momentum superconducting state |
| **BCS** | Bardeen-Cooper-Schrieffer — conventional uniform superconducting state |
| **BdG** | Bogoliubov-de Gennes — mean-field Hamiltonian for superconductors |

---

## 8. Further Reading

**Textbooks**
- Nielsen & Chuang, *Quantum Computation and Quantum Information* (2000) — the standard reference
- Preskill, *Lecture Notes on Quantum Computation* (freely available at [theory.caltech.edu](http://theory.caltech.edu/~preskill/ph229/)) — rigorous and current

**Introductions**
- Preskill, *Quantum Computing in the NISQ Era and Beyond* (2018, [arXiv:1801.00862](https://arxiv.org/abs/1801.00862)) — defines the era, sets expectations honestly
- Aaronson, *Quantum Computing Since Democritus* (2013) — accessible, intellectually honest

**Physics background**
- Tinkham, *Introduction to Superconductivity* — BCS theory, BdG formalism
- Casalbuoni & Nardulli, *Inhomogeneous superconductivity in condensed matter and QCD* (2004) — FFLO state review

**Software**
- [Qiskit documentation](https://docs.quantum.ibm.com/) — IBM's quantum SDK (used in all notebooks)
- [IQM documentation](https://docs.iqm.com/) — AaltoQ20 access
- [xfacpy / xfac](https://github.com/tensor4all/xfac) — QTCI tensor cross interpolation library

---

*This document was prepared as part of the **Quantum Algorithms for Condensed Matter Physics** project. See `README.md` for the full repository overview.*
