# Introduction

This tutorial will guide you through using CogniNest to conduct brain-constrained neural network simulations of cognitive phenomena. Out of the box, CogniNest is a brain-constrained neural network model of language in the brain, specifically suited to model semantics. However, it can be flexibly modified to correspond to other facets of cognition by following a brain-constrained modelling approach (see [Pulvermüller et al., 2021](https://doi.org/10.1038/s41583-021-00473-5)).

This tutorial covers the following aspects of CogniNest:

- Running a simulation:
	- Running a basic object vs action word simulation
	- Reading model diagnostics during simulation
	- Accessing and interpreting model output
- Code organisation:
	- Logical organisation of simulations
	- Order of simulation steps
- Designing custom experiments:
	- modifying simulation parameters
	- modifying the training routine
	- Tutorial: Training a 'congenitally blind' model
	- Tutorial: Lesioning the model

The tutorial will not cover documentation of the behind-the-scenes C++ code, which governs how excitatory and inhibitory neurons are implemented, and how synaptic plasticity is calculated.

## What should you know before getting started?
The examples in this tutorial require some background knowledge in Python, brain-constrained neural network modelling and navigating a Docker environment.

**Python**

- Variables and functions
- Constants
- Libraries
- Object-oriented programming/classes

**Brain-constrained neural network modelling**

- Long-term potentiation (LTP) and long-term depression (LTD)
- Membrane potentials
- Action potentials/spikes
- Brain constraints for cognitive modelling

**Docker**

- Running a container
- Mounting folders