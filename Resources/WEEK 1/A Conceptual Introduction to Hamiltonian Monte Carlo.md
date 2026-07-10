### Betancourt — "A Conceptual Introduction to Hamiltonian Monte Carlo" (2017)

**Type:** arXiv:1701.02434 · The standard intuition-building reference for HMC

**What we're looking for**
A clear, modern explanation of *why* HMC works and *why* it beats simpler samplers — the intuition we need to justify using HMC on the toys before scaling up to the reservoir readout.

**Plain-language summary**
This paper explains HMC from the ground up, focusing on intuition rather than heavy proofs. Its central point is *why* naive samplers fail: a random walk wastes most of its steps proposing values that don't fit the data, so it explores the posterior very slowly, especially as the number of parameters grows. HMC fixes this by using gradient information — the slope of the probability landscape — to propose moves that follow the shape of the posterior, so it reaches distant, still-probable states efficiently. It builds the picture of exploring a "typical set" (the region where the probability mass actually lives) and shows how the physics-inspired trajectories keep the sampler inside it.

**Which method we took from it**
The conceptual justification for choosing HMC over a random-walk sampler for all our Bayesian fitting. It is the reference behind statements like "HMC explores the posterior far more effectively than a simple random-walk sampler."

**How it connects to what we did**
Every toy we ran was sampled with HMC (via PyMC's NUTS), and each converged cleanly (R-hat = 1.00, 0 divergences) on the first attempt. This paper explains *why* that efficiency happens, and it is the citation we use when we motivate HMC as the right sampler for the readout — a setting with 100+ weights, where a random walk would struggle exactly as this paper describes.

**Why we cite it**
It is the standard reference for HMC intuition and for justifying HMC over simpler samplers in Chapter 3.
