----
title: Hartree-Fock SCF Program
authors: Yongbin Zhuang
date: 2020-05-24 22:47:19
mathjax: true
----

# Hartree Fock Self Consistent Field: A Simple Python Implementation

## Basic Review

### Hartree-Fock Equation

Hartree Fock Theory is a classic theory for solving many electron problem, which is useful in Chemistry, since molecules, nanoparticles and catalysts contain a lot of electrons. The theoretic background with detail deduction is referred to a very good book *Modern Quantum Chemistry*(Szabo). I will summarize the points here.

This theory approximates that the electrons move in the *averaged* field exerted from all the rest of electrons. Instead of using Hamilatonian to solve the schordinger equation:
$$
H\Psi = E\Psi
$$
We replace the Hamiltonian with the Fock operator, or the Fockian:
$$
F\psi_i = \epsilon_i\psi_i
$$
$\Psi$ is a wavefunction of whole system, $\psi_i$ is a wavefunction of a single orbital. Then System's wavefunction can be constructed from a list of occupied single orbital wavefunction via the *Slater determinant*.

Fockian contains the interactions that one electron will feels,

$$
F = \underbrace{T_i}_{\text{kinetic energy}} + \underbrace{\sum_{R}V_{nuc}(R)}_{\text{attraction from nuclear}} + \underbrace{\sum_j J(j) - K(j)}_{\text{ee repulsion}}
$$

### The Self Consistent Field (SCF)

In order to construct the Fockian, information about all the electron should be known at first which is what we need to know at last. To solve this problem, a initial guess for the electron's wavefunction {$\psi^0_i$} is needed. According to these wavefunction, a initial Fockian $F^0$ can be constructed, then a set of new wavefunctions can be solved {$\psi_i^1$}, then a Fokian 1 $F^1$ is constructed, etc.. 

### Matrix Form of Fockian: The Hartree-Fock-Roothan Equation

Usually we won't present a wavefunction by a long set of data, instead we use the basis functions. Every single electron wavefunction can be expanded in terms of basis function:
$$
\psi_i = \sum_\mu c_\mu \phi_\mu
$$
The basis function $\phi_\mu$ won't change during the calculation, but the coefficient {$c_\mu$} does. Then the Fockian can be projected into the space of basis function, then become a matrix:
$$
\textbf{F}\textbf{C}= \textbf{C}\epsilon
$$
For the non-orthogonal basis
$$
\textbf{F}\textbf{C}= \textbf{SC}\epsilon
$$
 we can orthogonalize the basis at first by find $\textbf{X}= \textbf{S}^{-1/2}$, then the following condition is fulfilled:
$$
\textbf{X}^\dagger\textbf{S}\textbf{X} = \textbf{I}
$$
Substitute $\textbf{X}\textbf{X}^{-1}$ into Fockian
$$
\textbf{X}^\dagger\textbf{FX}\textbf{X}^{-1}\textbf{C} = \textbf{X}^\dagger\textbf{SX}\textbf{X}^{-1}\textbf{C}\epsilon
$$

$$
\textbf{F}^\prime \textbf{C}^\prime = \textbf{C}^\prime\epsilon
$$

Where$\textbf{X}^\dagger\textbf{FX} = \textbf{F}^\prime$, $\textbf{X}^{-1}\textbf{C} = \textbf{C}^\prime$



## Programing

A home-made simple HF-SCF python program is [here](https://github.com/robinzyb/HFpy).

### The Recipe

Follow the recipe from *Modern Quantum Chemistry*, we use the simple sto-3g basis.

1. Give the system information: the element position, the element nuclear charge and the electron number of whole system. (`main.py`)
2. Calculate the basic integration: kinetic integral, nuclear attraction integral, electron repulsion integral (`integration.py`)
3. Obtain the $\textbf{X}$ (`math.py`)
4. Initial guess of a set of coefficient (i.e. a set of single electron wavefunction(`main.py`)
5. Obtain the density matrix(`integration.py`)
6. Obtain the fock and fock prime matirx( `integration.py`)
7. Diagonize and solve fock prime to get $\textbf{C}^\prime$ (`main.py`)
8. Get new $\textbf{C}$ from $\textbf{C}^\prime$ (`main.py`)
9. Compare the difference between old $\textbf{C}$ and new $\textbf{C}$. (`main.py`)(I took the matrix difference and select out the max difference)
10. Check whether the difference met the threshold requirement, if not, go back to fifth step (`main.py`)(you set threshold value)

