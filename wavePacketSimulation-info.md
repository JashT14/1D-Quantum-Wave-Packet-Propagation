# Wave Packet Simulation: Math and Physics Notes

This document explains the mathematics and physics behind `wave_packet_simulation.ipynb`.

## 1. Governing Equation

The notebook simulates a single nonrelativistic quantum particle in one spatial dimension using the time-dependent Schrodinger equation:

$$
i\hbar \frac{\partial \psi(x,t)}{\partial t}
=
\left(
-\frac{\hbar^2}{2m}\frac{\partial^2}{\partial x^2} + V(x)
\right)\psi(x,t)
$$

### Definitions

- `\psi(x,t)`: quantum wavefunction
- `|\psi(x,t)|^2`: probability density for position measurement
- `\hbar`: reduced Planck constant
- `m`: particle mass
- `V(x)`: potential energy function

In the notebook, natural units are used:

$$
\hbar = 1, \qquad m = 1
$$

This keeps the formulas simpler while preserving the physics.

## 2. Probability Interpretation

The wavefunction must be normalized:

$$
\int_{-\infty}^{\infty} |\psi(x,t)|^2 \, dx = 1
$$

This says the total probability of finding the particle somewhere in space is 1.

### Expectation values

The notebook computes observables from the wavefunction.

Position expectation value:

$$
\langle x \rangle = \int x |\psi(x,t)|^2 \, dx
$$

Momentum expectation value:

$$
\langle p \rangle = \int \psi^*(x,t)\left(-i\hbar\frac{\partial}{\partial x}\right)\psi(x,t)\,dx
$$

Position variance:

$$
\Delta x^2 = \langle x^2 \rangle - \langle x \rangle^2
$$

The width of the packet is then:

$$
\Delta x = \sqrt{\Delta x^2}
$$

## 3. Initial State: Gaussian Wave Packet

The notebook starts with a Gaussian packet:

$$
\psi(x,0) = A\exp\left[-\frac{(x-x_0)^2}{2\sigma^2}\right] e^{ik_0 x}
$$

### Meaning of parameters

- `x0`: initial center of the packet
- `sigma`: spatial width parameter
- `k0`: mean wavenumber, so the packet has an average momentum

The complex phase factor `e^{ik_0 x}` gives the packet motion.

### Physical interpretation

For a free particle, the mean momentum is approximately:

$$
\langle p \rangle = \hbar k_0
$$

and the group velocity is:

$$
v_g = \frac{\hbar k_0}{m}
$$

So if `k0 > 0`, the packet moves in the positive `x` direction.

### Example

If `k0 = 2`, `\hbar = 1`, and `m = 1`, then:

$$
v_g = 2
$$

So the center of the packet should move at about 2 units of `x` per unit time.

## 4. Why the Packet Spreads

A Gaussian packet is not a momentum eigenstate. It contains a distribution of momenta around `k0`.

For a free particle, the kinetic energy is:

$$
E(k) = \frac{\hbar^2 k^2}{2m}
$$

Different momentum components evolve at different phase rates, so the packet spreads over time. This is one of the central quantum effects shown in the notebook.

This spreading is the uncertainty principle in action:

$$
\Delta x \, \Delta p \gtrsim \frac{\hbar}{2}
$$

A narrow packet in position space must have a broad momentum distribution.

## 5. Numerical Method: Split-Step Fourier Method

The notebook evolves the wavefunction with the split-step Fourier method (SSFM). The key idea is to split the Hamiltonian into potential and kinetic parts:

$$
\hat{H} = \hat{T} + \hat{V}
$$

where:

$$
\hat{T} = -\frac{\hbar^2}{2m}\frac{\partial^2}{\partial x^2}, \qquad \hat{V} = V(x)
$$

The short-time evolution operator is approximated by Strang splitting:

$$
e^{-i\hat{H}\Delta t/\hbar}
\approx
e^{-i\hat{V}\Delta t/(2\hbar)}
e^{-i\hat{T}\Delta t/\hbar}
e^{-i\hat{V}\Delta t/(2\hbar)}
$$

### Why Fourier methods help

The kinetic operator is diagonal in momentum space. If `\tilde{\psi}(k,t)` is the Fourier transform of `\psi(x,t)`, then:

$$
\hat{T} \to \frac{\hbar^2 k^2}{2m}
$$

So the algorithm is:

1. Apply half of the potential phase in position space.
2. Fourier transform to momentum space.
3. Apply the kinetic phase there.
4. Transform back.
5. Apply the second half of the potential phase.

This method is efficient and approximately unitary, so it preserves normalization well.

## 6. Free Particle Case

For `V(x) = 0`, the Schrodinger equation becomes:

$$
i\hbar \frac{\partial \psi}{\partial t}
=
-\frac{\hbar^2}{2m}\frac{\partial^2 \psi}{\partial x^2}
$$

### Physics

- The packet center moves with constant velocity.
- The packet spreads in time.
- The expectation value `\langle x \rangle` follows classical motion.

### Ehrenfest theorem

The notebook checks:

$$
\frac{d\langle x \rangle}{dt} = \frac{\langle p \rangle}{m}
$$

For a free particle, `\langle p \rangle` is constant, so `\langle x \rangle` should increase linearly in time.

### Example

With `x0 = -15` and `k0 = 2`, the packet begins on the left side of the domain and moves to the right. The center should travel approximately as:

$$
\langle x \rangle(t) \approx x_0 + \frac{\hbar k_0}{m} t
$$

which becomes `-15 + 2t` in the notebook's units.

## 7. Harmonic Oscillator

The harmonic potential is:

$$
V(x) = \frac{1}{2}m\omega^2 x^2
$$

This is the quantum analog of a mass on a spring.

### Classical force

The force is the negative gradient of the potential:

$$
F(x) = -\frac{dV}{dx} = -m\omega^2 x
$$

So the particle is pulled back toward the origin.

### Expected motion

Ehrenfest's theorem gives:

$$
\frac{d^2\langle x \rangle}{dt^2} = -\omega^2 \langle x \rangle
$$

That is the equation of a classical harmonic oscillator.

### Important note

The notebook starts the packet with zero initial momentum, displaced from the origin. If the width is not exactly the ground-state width, the packet can breathe or slightly distort while oscillating. So the center follows classical-like motion, but the full packet remains quantum.

### Example

If `\omega = 0.3`, the classical period is:

$$
T = \frac{2\pi}{\omega} \approx 20.94
$$

So the packet should oscillate with a period of about 21 time units.

## 8. Potential Barrier and Tunneling

The notebook also studies a rectangular barrier:

$$
V(x) =
\begin{cases}
V_0, & |x-x_c| < w/2 \\
0, & \text{otherwise}
\end{cases}
$$

### Physics

Classically, a particle with energy less than `V0` cannot cross the barrier.
Quantum mechanically, the wavefunction can penetrate and partially transmit through the barrier. This is tunneling.

### Transmission and reflection

The notebook estimates transmission and reflection by integrating the final probability density on the right and left sides of the barrier:

$$
T \approx \int_{x > x_c + w/2} |\psi(x,t_f)|^2 \, dx
$$

$$
R \approx \int_{x < x_c - w/2} |\psi(x,t_f)|^2 \, dx
$$

If the simulation is run long enough and the wave packet has separated from the barrier region, then `T + R` should be close to 1.

### Example

If the mean kinetic energy is below the barrier height, you usually see mostly reflection with a small transmitted tail. If the barrier is low, transmission increases.

## 9. Discrete Grid and FFT Details

The notebook uses a finite interval `[-L, L)` with `N` grid points.

### Spatial grid

$$
dx = \frac{2L}{N}
$$

### Momentum grid

The momentum values are represented using FFT frequencies. This is consistent with periodic boundary conditions on the numerical domain.

### Practical meaning

The simulation is reliable when:

- the wave packet stays away from the boundaries
- the timestep `dt` is small enough
- the momentum distribution fits inside the FFT bandwidth

If the packet reaches the boundary, the periodic FFT assumption can cause unphysical wraparound.

## 10. What Each Diagnostic Means

The notebook plots several quantities.

### `|psi(x,t)|^2`
This is the position-space probability density. It shows where the particle is likely to be found.

### `\langle x \rangle(t)`
This is the average position. It is the quantum analog of the classical trajectory of the center of mass.

### `\sigma(t)`
This is the packet width or spread. It measures how much the state is dispersing.

### Norm
The quantity

$$
\int |\psi|^2 dx
$$

should remain close to 1. This checks numerical stability and probability conservation.

## 11. Short Physical Summary

This notebook demonstrates the following ideas:

- a wavefunction is a probability amplitude, not a classical path
- a Gaussian packet behaves like a localized particle with quantum spread
- free packets move with group velocity and spread in time
- harmonic potentials produce oscillatory motion of the expectation value
- barriers can reflect and transmit wave packets through tunneling
- split-step Fourier methods provide a practical numerical solver for the TDSE

## 12. Quick Example Interpretations

### Free particle
If `k0 > 0`, the packet moves right. If `k0 < 0`, it moves left.

### Larger `sigma`
A larger spatial width means a narrower momentum spread, so the packet spreads more slowly.

### Higher barrier height
A higher barrier reduces transmission.

### Wider barrier
A wider barrier also reduces transmission, often very strongly.

## 13. Bottom Line

The notebook is a compact numerical study of quantum dynamics in 1D. It ties together:

- the Schrodinger equation
- Gaussian wave packets
- expectation values
- Ehrenfest's theorem
- tunneling
- harmonic motion
- Fourier-based time stepping

It is a good example of how classical-looking motion emerges from quantum mechanics while the full wavefunction still evolves according to the linear TDSE.
