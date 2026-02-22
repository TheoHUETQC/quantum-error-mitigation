# Quantum Error Mitigation

This repository contains a numerical study of quantum error mitigation techniques, with a focus on **Zero-Noise Extrapolation (ZNE)**, implemented using **Qiskit**.

The project was carried out as part of a Master 2 course in *Quantum Computation and Error-Control Codes*. The oral presentation was based on the article:

> Error Mitigation for Short-Depth Quantum Circuits
K. Temme, S. Bravyi, J. M. Gambetta (2017)

In our presentation:

- My classmate presented the Probabilistic Error Cancellation (PEC) method.
- I focused on **Zero-Noise Extrapolation (ZNE)** and reproduced key results numerically.

---

## 1. What is Quantum Error Mitigation?

Quantum error mitigation (QEM) refers to a family of techniques designed to **reduce the effect of noise in near-term quantum devices (NISQ devices)** without requiring full fault tolerance.

Unlike quantum error correction (QEC), error mitigation:

- Does not encode logical qubits into many physical qubits.
- Does not require syndrome extraction.
- Does not require active correction cycles.
- Works at the expectation value level, not at the state level.

The goal is not to remove noise physically, but to estimate what the noiseless expectation value would have been.

This makes QEM particularly relevant for:

- Variational quantum algorithms
- Short-depth circuits
- NISQ-era hardware

---

## 2. Error Mitigation vs Quantum Error Correction

Quantum Error Correction (QEC)

- Encodes logical qubits into many physical qubits.
- Detects and corrects errors using redundancy.
- Requires large overhead.
- Enables fault-tolerant quantum computation.

Quantum Error Mitigation (QEM)

- No logical encoding.
- No active correction.
- Low overhead.
- Provides improved expectation values.
- Valid only in the weak noise regime.

In short:

> QEC enables scalable fault tolerance.
>
> QEM improves results on noisy intermediate-scale quantum devices.

---

## 3. The Article: Two Mitigation Methods

The referenced article introduces two main techniques:

### 3.1 Probabilistic Error Cancellation (PEC)

- Expresses noisy gates as linear combinations of ideal operations.
- Requires quasi-probabilities (which may be negative).
- In principle can exactly recover noiseless expectation values.
- Comes at the cost of increased sampling overhead.

This part was presented by my classmate.

### 3.2 Zero-Noise Extrapolation (ZNE)

- Assumes the noise is weak and stable.
- Artificially amplifies the noise.
- Measures the observable at different noise strengths.
- Extrapolates back to the zero-noise limit.

This is the method implemented in this repository.

---

## 4. Zero-Noise Extrapolation (ZNE)

### Core Idea

If the expectation value admits a **perturbative expansion** in the noise parameter $p$:

$$ E(p)=E^∗+a_1 p+a_2p^2+O(p^3)$$

then we can:

1. Evaluate the observable at different effective noise strengths.
2. Use **Richardson extrapolation**.
3. Cancel the linear term.
4. Estimate $E^∗$.

### Project

For this project, in order to follow the hypotheses of the article, we will use a low-depth circuit.

<img src="https://github.com/TheoHUETQC/quantum-error-mitigation/blob/main/figure/circuit.png?raw=1" width="500" alt="Quantum Circuit">

And we will study, as a quantity, the correlation between the two qubits, $\langle Z_0 Z_1\rangle$.

### Noise Amplification via Local Folding

To increase the effective noise strength without changing the ideal circuit, in this project we use **local gate folding**:

$$ G \rightarrow GG^\dagger G$$

Ideally:

$$GG^\dagger G=G$$

But physically, noise acts after each gate, so the total noise is amplified.

<img src="https://github.com/TheoHUETQC/quantum-error-mitigation/blob/main/figure/folded_circuit_scale3.png?raw=1" width="1200" alt="Folded Quantum Circuit">

We evaluate:

- $E_1$ (original circuit)
- $E_3$ (folded once)
- $E_5$ (folded twice)

### Richardson Extrapolation (Order 1)

Using scales 1 and 3:

$$ E_{ZNE} = \frac{3E_1 - E_3}{2}$$

This cancels the linear term in $p$, leaving:

$$ E_{ZNE} −E^∗=O(p^2)$$

---

## 5. Results of the numerical Study

Summary of this project:

- A parameterized 2-qubit circuit was implemented in Qiskit.
- Depolarizing noise was added to single- and two-qubit gates.
- The observable $\langle Z_0 Z_1\rangle$ was measured.
- ZNE was implemented using local folding.
- Richardson extrapolation was applied.

### Main Result

<img src="https://github.com/TheoHUETQC/quantum-error-mitigation/blob/main/figure/expectation_value_p.png?raw=1" width="500" alt="Expectation_value(p)">

- Without mitigation:

$$ ∣E(p)−E^∗∣ \propto p \qquad \text{(the Green curve)}$$

- With ZNE (order $n=1$):

$$ ∣E_{ZNE}(p)−E^∗∣ \propto p^2 \qquad \text{(the Blue curve)}$$

<img src="https://github.com/TheoHUETQC/quantum-error-mitigation/blob/main/figure/proportionality_error.png?raw=1" width="500" alt="Proportionality error">

This quadratic scaling was confirmed numerically.

Graphical results show:

- Linear growth of the raw error.
- Quadratic growth of the mitigated error.
- Clear suppression of the leading-order noise contribution.

---

## 6. Why This Is Interesting

- Demonstrates that near-term quantum algorithms can be improved without full error correction.
- Confirms the perturbative noise model assumed in the literature.
- Shows concretely how extrapolation cancels first-order noise contributions.
- Provides a bridge between theoretical QEC concepts and practical NISQ implementations.

Zero-Noise Extrapolation is simple to implement, requires no additional qubits, and works well in the weak-noise regime, but it becomes unstable when the noise is no longer perturbative.

---

## 7. Conclusion

This project reproduces and illustrates key ideas from:

> Temme, Bravyi, Gambetta — Error Mitigation for Short-Depth Quantum Circuits

The main takeaway:

- In the weak-noise regime, expectation values admit a perturbative expansion.
- ZNE cancels the linear noise contribution.
- The error scaling improves from $O(p)$ to $O(p^2)$.

This confirms both the theoretical mechanism and its practical effectiveness in simple quantum circuits.

---

## References

- **Error Mitigation for Short-Depth Quantum Circuits**
Kristan Temme, Sergey Bravyi, and Jay M. Gambetta
*IBM T. J. Watson Research Center, Yorktown Heights, New York 10598, USA*
(Received 21 July 2017; published 3 November 2017);
[DOI](https://doi.org/10.1103/PhysRevLett.119.180509), [arXiv](https://arxiv.org/abs/1612.02058),

- Yunos EL KADERI's course on **Quantum Computation and Error-Control Codes** (Master 2),

- [IBM Qiskit documentation](https://quantum.cloud.ibm.com/docs/en/guides),

- [Numpy documentation](https://numpy.org/doc/),

- [Matplotlib documentation](https://matplotlib.org/stable/index.html).