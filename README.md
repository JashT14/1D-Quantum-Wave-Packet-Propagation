# 1D-Quantum-Wave-Packet-Propagation
using schrodingers, we generate gaussian wave packet and extract classical motions from expectation values
# Wave Packet Simulation

This project demonstrates 1D quantum wave packet propagation using the time-dependent Schrodinger equation.

Notebook: `wavePacketSimulation.ipynb`

## What it shows

- Gaussian wave packet motion
- Quantum spreading over time
- Expectation values like `⟨x⟩`, `⟨p⟩`, and `σ(t)`
- Free-particle motion, harmonic motion, and barrier tunneling
- Ehrenfest check (`d⟨x⟩/dt` vs `⟨p⟩/m`)
- Normalization tracking for numerical stability

## How it works

The notebook uses the split-step Fourier method (SSFM) to evolve the wavefunction in time.

Main flow in the notebook:

1. Parameter setup (`hbar`, `m`, grid, timestep)
2. Core functions (Gaussian initializer, potentials, SSFM solver, observables)
3. Free-particle simulation + summary plots + animation
4. Harmonic oscillator simulation
5. Barrier tunneling/reflection simulation
6. Ehrenfest consistency check
7. Sandbox cell for custom scenarios

## More details

For the full math and physics explanation, see [wavePacketSimulation-info.md](wavePacketSimulation-info.md).