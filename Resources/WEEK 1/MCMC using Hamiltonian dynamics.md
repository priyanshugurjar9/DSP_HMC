### Neal — "MCMC using Hamiltonian dynamics" (2011) — [Ref 8]

**Type:** Book chapter · *Handbook of Markov Chain Monte Carlo*, Ch. 5, pp. 113–162 (Chapman & Hall/CRC) · The leapfrog integrator and energy formulation are in the early sections of the chapter (verified: Ch. 5, pp. 113–162)

**What we're looking for**
The foundational explanation of *how* HMC moves through parameter space — the energy idea we kept getting asked about — so we can describe our sampler correctly in the thesis.

**Plain-language summary**
This chapter is the standard reference for HMC. Its key idea is an analogy with physics. Imagine the parameter values as the position of a ball rolling over a landscape of hills and valleys. The valleys are the parameter values that fit the data well (high probability). The method gives the ball a random push (a **momentum**), then lets it roll according to Hamiltonian mechanics, which trade **potential energy** (height on the landscape — how badly the parameters fit) against **kinetic energy** (the motion). Because a rolling ball can travel a long way before being stopped, HMC proposes distant new states that are still probable, letting it explore far more efficiently than a random walk. The trajectory is computed numerically with the **leapfrog** integrator, which alternates small updates to position and momentum.

**Which method we took from it**
The conceptual model of HMC we use throughout: HMC is a **sampler** that finds good parameter values; the energy/kinetic/potential machinery is only the search mechanism, not part of the prediction. This is the explanation we use whenever we describe why our readout is fitted "by HMC".

**How it connects to what we did**
Every toy we ran (coin, linear, logistic) was sampled by HMC — specifically the No-U-Turn variant inside PyMC, which automates the step-size and trajectory-length choices Neal describes by hand. When we reported "0 divergences" and "R-hat = 1.00", those diagnostics check exactly the numerical behaviour (stable leapfrog trajectories, well-explored posterior) this chapter sets out.

**Methodology illustration**
Conceptually, one leapfrog step updates momentum a half-step, position a full step, then momentum another half-step (done internally by PyMC, not coded by us):

```python
# conceptual leapfrog step
p = p - 0.5*eps * grad_U(q)   # half step on momentum
q = q + eps * p               # full step on position
p = p - 0.5*eps * grad_U(q)   # half step on momentum
```

**Why we cite it**
It is *the* reference for the HMC method itself in Chapter 3 of the thesis.
