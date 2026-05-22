---
title: "What's the post-Minkowskian expansion?"
date: 2025-03-19
description: "A short explainer on the post-Minkowskian expansion in gravitational two-body scattering."
tags: [explainer, gravitational-waves, qft]
---

When physicists do calculations of processes in nature, for example collisions at the Large Hadron Collider (LHC), it turns out to be pretty close to impossible to calculate things *exactly*. This is because when you write down all your forces and contributions, you end up with one or more differential equations that no one knows the answer to. There are generally two types of solutions to this. The first is to solve it all numerically. For those LHC processes, this is called [lattice field theory](https://en.wikipedia.org/wiki/Lattice_field_theory), because you discretize spacetime, put your system on a lattice, and solve it numerically there. In general relativity, it's a whole field called [numerical relativity](https://en.wikipedia.org/wiki/Numerical_relativity), and typically we consider it to be the "ground truth" that we compare all our calculations to because it's so accurate for almost any process. The downside? It's really, really expensive to run. One single, simulation of a black hole merger takes around



Typically, scattering calculations — as in regular quantum field theory — are done order-by-order in perturbation theory. In our setting, the small parameter is Newton's constant $G$. The resulting expansion is called the **post-Minkowskian (PM) expansion**, because we start from flat Minkowski spacetime and add gravitational corrections one power of $G$ at a time.
