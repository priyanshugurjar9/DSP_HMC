### McElreath — *Statistical Rethinking* (2nd ed., 2020) 

**Type:** Book, Chapman & Hall/CRC · HMC/toy material in the MCMC chapter (Ch. 9 — *verify in your copy*)

**What we're looking for**
A clear, worked introduction to Bayesian inference and to sampling from a posterior, written for people who are not statisticians. This is the single most useful source for our toy examples.

**Plain-language summary**
The book teaches Bayesian modelling by building small models and fitting them, rather than by proving theorems. Its core message is that every model has three parts:
- **Prior** — what you believe before seeing data
- **Likelihood** — how the data are generated given the parameters
- **Posterior** — your updated belief after seeing the data

The posterior is what we care about, and for anything beyond the simplest models it cannot be written down exactly — so we *sample* from it instead. The book shows that Hamiltonian Monte Carlo (via Stan) is the practical way to draw those samples, and it teaches how to read the diagnostics that tell you whether the sampling worked: **R-hat** (should be ≈ 1.00), **effective sample size**, and **trace plots** that should look like a fuzzy, well-mixed band.

**Which method we took from it**
The entire structure of our three toy examples: define a prior → define a likelihood → sample the posterior → check recovery and convergence. Our coin-flip example is the book's canonical first example (estimating a proportion); our linear-regression toy follows its regression chapter directly.

**How it connects to what we did**
In Week 2 we generated 50 coin flips with a known bias of 0.7, placed a Uniform(0,1) prior on the bias, used a Bernoulli likelihood, and sampled with HMC. We recovered a posterior mean of **0.653**, a 94% credible interval of **[0.531, 0.772]**, and **R-hat = 1.00** — exactly the workflow this book teaches.

```python
true_p = 0.7
data = np.random.binomial(1, true_p, size=50)

with pm.Model() as m:
    p = pm.Uniform('p', 0, 1)                    # prior
    y = pm.Bernoulli('y', p=p, observed=data)    # likelihood
    idata = pm.sample(2000, tune=1000, chains=4) # HMC

az.summary(idata)   # -> mean 0.653, hdi [0.531, 0.772], r_hat 1.00
```

**Why we cite it**
It is the authority for our toy methodology and for how we read convergence diagnostics.
