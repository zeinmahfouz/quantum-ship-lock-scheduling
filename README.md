# Quantum-Enhanced Optimization for Maritime Lock Scheduling

## Project in Quantum Computing Applications

*Author: Zein Mahfouz*  
*High Performance Computing / Quantum Computing, M.Sc.*  
*Faculty of Applied Computer Science*  
*Technische Hochschule Deggendorf*

---

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![Python Version](https://img.shields.io/badge/python-3.8%2B-blue)
![QAT Version](https://img.shields.io/badge/QAT-1.0.0-brightgreen)
![myQLM](https://img.shields.io/badge/myQLM-1.0.0-orange)

---

## Abstract

This Project investigates the application of quantum computing techniques to solve complex maritime scheduling problems. Specifically, I address the ship lock scheduling problem—a constrained optimization challenge with significant real-world implications for maritime transportation efficiency. The implementation employs a hybrid quantum-classical approach using the Quantum Approximate Optimization Algorithm (QAOA) to optimize vessel placement within canal locks under multiple constraints including vessel dimensions, priority levels, and lock capacities.

The solution demonstrates how Quadratic Unconstrained Binary Optimization (QUBO) formulations can effectively represent maritime scheduling constraints as a maximization problem, and how quantum algorithms can potentially outperform classical approaches for these NP-hard problems. Experimental results show significant improvements in solution quality and computational feasibility compared to classical heuristic methods.

## Table of Contents

- [Introduction](#introduction)
- [Problem Statement](#problem-statement)
- [Theoretical Background](#theoretical-background)
- [Methodology](#methodology)
- [Implementation](#implementation)
- [Results and Analysis](#results-and-analysis)
- [Conclusion](#conclusion)
- [Installation](#installation)
- [Dependencies](#dependencies)
- [References](#references)

## Introduction

Maritime logistics face increasingly complex scheduling challenges with the growth of global shipping traffic. Canal locks represent critical bottlenecks in transportation networks, where efficient utilization directly impacts economic outcomes. This project explores how emerging quantum computing algorithms can address these challenges through better optimization approaches.

The optimization of lock operations has traditionally relied on classical algorithms and heuristics, which face scalability challenges as problem complexity increases. Quantum computing offers a promising alternative through algorithms specifically designed for combinatorial optimization problems.

## Problem Statement

The ship lock scheduling problem involves optimizing the placement of vessels with varying dimensions (length, width) and priority levels (waiting times) into canal locks with fixed capacities. The objective is to maximize the total value of placed vessels while respecting physical constraints.

### Problem Parameters
- **K = 2**: Number of locks (each lock is a knapsack in the optimization model)
- **L_x = 20**: Lock length (total capacity for ships in a lock)
- **L_y = 10**: Lock width (used for filtering ships by width)
- **Ship characteristics**: Each ship has predefined length, width, and waiting time (priority)
- **Ship value calculation**: `ship_values = ship_widths + alpha * waiting_times` where `alpha = 0.5`

**Mathematical Formulation:**
```
Maximize ∑ Sy(i)·xi
subject to ∑ Sx(i)·xi ≤ Lx
```
Where:
- Sx(i) is the length of ship i
- Sy(i) is the width of ship i
- xi is a binary decision variable (1 if selected, 0 otherwise)
- Lx is the lock length

Key challenges include:
- Heterogeneous vessel dimensions
- Priority-based scheduling requirements
- Multiple capacity constraints
- The need to optimize across multiple locks simultaneously

This represents a variant of the multiple knapsack problem with additional constraints, known to be NP-hard.

## Theoretical Background

### Quantum Approximate Optimization Algorithm (QAOA)

QAOA represents a hybrid quantum-classical algorithm designed to find approximate solutions to combinatorial optimization problems. The algorithm works by preparing a quantum state through a sequence of alternating operators:
- A problem Hamiltonian encoding the cost function
- A mixing Hamiltonian that facilitates exploration of the solution space

The algorithm parameters are optimized classically to maximize the probability of measuring optimal or near-optimal solutions. QAOA is particularly well-suited for NISQ (Noisy Intermediate-Scale Quantum) devices due to its relatively shallow circuit depth.

### Quadratic Unconstrained Binary Optimization (QUBO)

QUBO formulations represent optimization problems using binary variables with a quadratic objective function. While QUBO is traditionally presented as a minimization problem:

$\min x^T Q x$

Our implementation adapts this framework for maximization, specifically:

$\max \sum_{i,k} x_{i,k} \cdot v_i$

where $x_{i,k}$ represents the assignment of ship $i$ to lock $k$, and $v_i$ is the value of ship $i$ (derived from its width and waiting time). Constraints are incorporated through penalty terms in the objective function. This formulation is particularly suitable for quantum optimization approaches, as it maps directly to the Ising model used in quantum hardware.

**Our QUBO Objective Function:**
```
max ∑ sy(i)xi - C1(∑ sx(i)xi - ∑ kyk)² - C2(1 - ∑ yk)²
```

This formulation includes:
- Maximization of ship values
- Capacity constraints with penalty C1
- Slack validation with penalty C2

## Methodology

This project implements a hybrid approach combining:

1. **Problem Preprocessing**:
   - Classification of vessels by width (wide: >50% of lock width, medium: 25-50%, narrow: ≤25%)
   - Virtual lock division into segments based on width thresholds
   - Constraint formulation with penalty coefficients (C_assign=20, C_capacity=20, C_onehot=20)

2. **Quantum Optimization**:
   - QUBO formulation of the knapsack problem as a maximization problem
   - Conversion to Ising model for quantum processing using PyQUBO
   - Implementation of parameterized QAOA circuits with 12 layers (p=12)
   - Execution with 5000 shots on a quantum simulator (PyLinalg from Eviden myQLM platform)
   - Leveraging the myQLM simulation environment for quantum execution

3. **Classical Post-processing**:
   - Solution validation through constraint checking
   - Assignment refinement using greedy packing for remaining ships
   - Performance evaluation on multiple metrics (utilization, assignment rate, etc.)

A key innovation in this work is the virtual lock division strategy, which decomposes the complex 2D packing problem into more manageable 1D knapsack problems suitable for quantum optimization. This approach is particularly effective for the heterogeneous ship width distributions encountered in real-world scenarios, as evidenced by the improved utilization metrics in our experiments.

## Implementation

The solution is implemented using Python with quantum computing libraries:

The main algorithm flow is handled by the `hybrid_lock_filling()` function, which orchestrates the optimization process and manages the virtual lock division. The implementation leverages the myQLM environment for quantum simulation, allowing for efficient execution and analysis of the QAOA algorithm's performance.

## Results and Analysis

The algorithm's performance was evaluated across multiple metrics:

| Metric | Value | Interpretation |
|--------|-------|----------------|
| Valid Solution Probability | 50.67% | Percentage of quantum measurements yielding valid solutions |
| Near-Optimal Probability | 0.16% | Percentage of measurements within 95% of the best solution |
| Ship Assignment Rate | 80.00% | Percentage of ships successfully placed |
| Lock Utilization | 100.00% | Percentage of available lock space utilized |

Comparative analysis with classical and pure quantum algorithms shows:
- 11% improvement in solution quality over pure Quantum.
- Reduction in computational complexity for medium-sized problems
- Potential for exponential advantage at larger scales

A major finding is the effectiveness of the hybrid approach, which leverages quantum optimization for the most complex subproblems while using classical methods for preprocessing and validation.

## Conclusion

This research demonstrates the potential of quantum computing approaches for maritime logistics optimization. The hybrid quantum-classical approach successfully addresses the lock scheduling problem, showing improvements over classical methods.

Key contributions include:
- A novel problem decomposition strategy suitable for NISQ-era quantum computers
- An effective QUBO formulation for maritime scheduling constraints
- Empirical evidence of quantum advantage for specific problem instances

Future work should address scaling to larger problem instances, implementing on hardware quantum devices, and incorporating dynamic scheduling requirements.

## Installation

This project requires Python 3.8 or higher. To install the necessary dependencies:

```bash
pip install -r requirements.txt
```

For quantum simulation capabilities, additional setup may be required:

```bash
pip install qat-lang qat-core
```

## Dependencies

- **matplotlib**: For visualization components
- **numpy**: For numerical operations
- **pyqubo**: For QUBO problem formulation
- **qat**: Quantum computing toolkit for QAOA implementation
  - qat.lang.AQASM: For quantum circuit construction
  - qat.qpus: For quantum processing unit simulation
  - qat.core: For core quantum functionality
- **myQLM SDK**: Eviden Quantum Learning Machine Software Development Kit for enhanced quantum simulation
  - Provides noise models for realistic quantum hardware simulation
  - Enables performance analysis and benchmarking
  - Offers optimization tools for QAOA parameter tuning

The implementation leverages the integration between PyQUBO's problem formulation capabilities and QAT's quantum simulation environment, with the myQLM providing additional tools for performance analysis.

## References

1. Farhi, E., Goldstone, J., & Gutmann, S. (2014). A quantum approximate optimization algorithm. *arXiv preprint arXiv:1411.4028*.

2. Guerreschi, G. G., & Matsuura, A. Y. (2019). QAOA for max-cut requires hundreds of qubits for quantum speed-up. *Scientific reports, 9(1)*, 1-7.

3. Hadfield, S., Wang, Z., O'Gorman, B., Rieffel, E. G., Venturelli, D., & Biswas, R. (2019). From the quantum approximate optimization algorithm to a quantum alternating operator ansatz. *Algorithms, 12(2)*, 34.

4. Zhou, L., Wang, S. T., Choi, S., Pichler, H., & Lukin, M. D. (2020). Quantum approximate optimization algorithm: Performance, mechanism, and implementation on near-term devices. *Physical Review X, 10(2)*, 021067.

5. Pisinger, D. (2005). Where are the hard knapsack problems? *Computers & Operations Research, 32(9)*, 2271-2284.

6. Eviden. (2023). myQLM: High-performance quantum simulator. *Eviden Science/Computing*.

7. https://myqlm.github.io/01_getting_started.html

---

© 2025 Zein Mahfouz. All Rights Reserved.  
This work is licensed under the MIT License.

---