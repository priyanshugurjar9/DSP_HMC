### Gelman, Carlin, Stern, Dunson, Vehtari & Rubin — *Bayesian Data Analysis* (3rd ed., 2013)

**Type:** Book, Chapman & Hall/CRC · Directly relevant chapters: single-parameter models incl. the binomial (coin), regression models (linear), and generalized linear models incl. logistic regression (*verify chapter/page in your copy*)

**What we're looking for**
The standard, authoritative reference for the exact three models our toys use — estimating a proportion (coin), Bayesian linear regression, and Bayesian logistic regression — so each toy has a textbook citation behind it.

**Plain-language summary**
This is the classic graduate reference for Bayesian data analysis. It works through, in order, exactly the model types our toys implement:
- **The binomial model** for estimating an unknown proportion from yes/no data — this is our coin-flip toy.
- **Linear regression** in a Bayesian framework, placing priors on slope, intercept, and noise — this is our linear toy.
- **Generalized linear models**, of which **logistic regression** is the standard example for binary outcomes — this is our logistic toy.

It also covers the practical machinery we rely on: posterior simulation, convergence monitoring, and effective sample size. Its third edition explicitly adds a presentation of Hamiltonian Monte Carlo, which is the sampler we use.

**Which method we took from it**
The textbook formulations of all three toy models. Where McElreath gives the hands-on recipe, this book gives the standard, citable statement of each model — the binomial for the coin, the Gaussian linear model for the linear toy, and the logistic GLM for the logistic toy.

**How it connects to what we did**
Each of our three toys maps to a specific model in this book:
- Coin → binomial/proportion model → recovered *p* = 0.653, HDI [0.531, 0.772]
- Linear → Bayesian linear regression → recovered slope ≈ 2.0, intercept ≈ −1.0, σ ≈ 0.5
- Logistic → logistic GLM → recovered slope ≈ 1.26, intercept ≈ −2.10

```python
# logistic toy — the GLM case from BDA, in miniature
true_slope, true_intercept = 1.5, -2.0
x = np.linspace(-4, 4, 60)
prob = 1 / (1 + np.exp(-(true_slope*x + true_intercept)))  # logistic link
y = np.random.binomial(1, prob)

with pm.Model() as logistic:
    slope     = pm.Normal('slope', 0, 5)
    intercept = pm.Normal('intercept', 0, 5)
    obs = pm.Bernoulli('obs', logit_p=slope*x + intercept, observed=y)
    idata = pm.sample(2000, tune=1000, chains=4)   # -> slope 1.26, intercept -2.10
```

**Why we cite it**
It is the standard textbook reference for the three model types our toys are built on, and it supports our use of HMC and convergence diagnostics.
