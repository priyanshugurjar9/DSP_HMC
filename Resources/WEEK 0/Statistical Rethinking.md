### McElreath — *Statistical Rethinking* (2nd ed., 2020): the HMC chapter

**Where:** Chapter 9, "Markov Chain Monte Carlo"
- 9.1 Good King Markov and his island kingdom
- 9.2 Metropolis algorithms
- **9.3 Hamiltonian Monte Carlo** ← the section our method comes from
- 9.4 Easy HMC: `ulam`
- 9.5 Care and feeding of your Markov chain ← the diagnostics we use

---

#### The problem the chapter sets up

What I took from this chapter is *why* we need a sampler at all. In Bayesian inference we want the posterior — our updated belief about the parameters after seeing the data. For very simple models you can write the posterior down exactly. For anything realistic you cannot: the maths becomes intractable. So instead of computing the posterior, we **draw samples from it**, and the collection of samples *becomes* our answer. Chapter 9 is entirely about how to draw those samples well.

#### The story of King Markov (§9.1–9.2) — and why it isn't good enough

McElreath explains Metropolis sampling through a parable. King Markov must visit his ring of islands in proportion to each island's population. He does this with a simple rule: propose moving to a neighbouring island at random; if the proposal has a bigger population, always go; if smaller, go only with a probability equal to the ratio of populations. Repeat forever. Remarkably, the fraction of time the King spends on each island ends up proportional to its population — without him ever knowing the whole map.

This is the Metropolis algorithm, and it works. **But here is the key limitation I took from it:** it is a *random walk*. It proposes blindly, so many proposals get rejected, and it explores slowly. And it gets dramatically worse as the number of parameters grows — the chapter shows that in high dimensions the probability mass moves *away from the peak*, so a sampler that wanders randomly near the peak spends its time in the wrong place. This is exactly the situation our project is in: our reservoir readout has 100+ parameters. A random walk would be hopeless.

#### Hamiltonian Monte Carlo (§9.3) — the fix

Section 9.3 introduces HMC through a second parable. King Markov's cousin **Monty** rules a *continuous* valley, not discrete islands, and has an advisor named **Hamilton**. Hamilton's insight is that instead of hopping randomly, you should *drive back and forth along the valley*, slowing the vehicle down where the houses are dense and speeding up where they are sparse. Do that, and you naturally spend time in each region in proportion to how many people live there.

That parable is the whole idea of HMC, and here is what it means mechanically, which is what I actually needed to understand:

- The parameter values are treated as the **position** of a particle on a landscape.
- The landscape's height is the **potential energy** — high where the parameters fit the data badly, low (valleys) where they fit well.
- The sampler gives the particle a random push — its **kinetic energy** / momentum.
- The particle then **rolls according to physics**, trading potential for kinetic energy, and where it stops becomes the proposed new sample.

Crucially, HMC uses the **gradient** of the landscape — it knows which way is downhill. That is why it can travel a long way and still land somewhere probable, unlike Metropolis, which proposes blindly. The chapter is explicit about the trade-off: **HMC is more expensive per step, but each step is far more useful, so it needs far fewer samples — and the advantage grows with the number of parameters.**

The trajectory is computed numerically, in small steps, by the **leapfrog integrator** (which McElreath implements in the chapter's "Overthinking" box, so you can see it isn't magic).

The one real restriction, stated plainly in the chapter: **HMC needs continuous parameters**, because it needs a gradient — it cannot glide through discrete ones.

#### Why this matters for *our* project

This is the section that justifies our entire method choice. Our readout has ~100 continuous weights. Metropolis would explore that space far too slowly. HMC's gradient-guided trajectories are precisely what make inference over that many weights feasible. When our thesis says "the readout is fitted by HMC," §9.3 is the reason why HMC and not something simpler.

#### Easy HMC (§9.4) and the diagnostics (§9.5) — what we actually use

Section 9.4 shows you don't hand-code HMC: you declare the model and let the software sample it (McElreath uses `ulam`/Stan; we use PyMC's NUTS, which is the same idea with automatic tuning).

Section 9.5 is the one I use every single time I run a model. It tells you how to check the sampling actually worked:

| Diagnostic | What it means | What we got |
|---|---|---|
| **R-hat** | Do the independent chains agree? Should be ≈ 1.00 | **1.00** on every toy |
| **Effective sample size** | How many *useful*, non-correlated samples | 2,000–5,500 |
| **Trace plot** | Should look like a fuzzy, well-mixed caterpillar | ✓ |
| **Divergences** | Failed trajectories — should be zero | **0** |

#### The method, applied — our coin toy

This is §9.3 + §9.5 in practice: declare prior and likelihood, let HMC sample, then check the diagnostics from §9.5.

```python
true_p = 0.7
data = np.random.binomial(1, true_p, size=50)

with pm.Model() as m:
    p = pm.Uniform('p', 0, 1)                    # prior
    y = pm.Bernoulli('y', p=p, observed=data)    # likelihood
    idata = pm.sample(2000, tune=1000, chains=4) # HMC (NUTS)

az.summary(idata)
# p: mean 0.653 | hdi [0.531, 0.772] | ess 3275 | r_hat 1.00 | 0 divergences
```

The posterior mean (0.653) recovers the true bias (0.7); the interval expresses how *unsure* we are; R-hat = 1.00 confirms it converged. That triple — **recover, quantify, verify** — is the workflow this chapter teaches, and it is the workflow we apply unchanged when the parameter is no longer a coin bias but the reservoir's readout weights.

**Why we cite it:** §9.3 is our reference for HMC's mechanism and for why it beats random-walk samplers in high dimensions; §9.5 is our reference for the convergence diagnostics we report throughout.
