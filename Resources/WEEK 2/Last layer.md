### Fiedler & Lucia — "Improved uncertainty quantification for neural networks with Bayesian last layer" (2023)

**Type:** arXiv:2302.10975 · Also published in IEEE Access (2023)
**Authors:** Felix Fiedler, Sergio Lucia (TU Dortmund)

**What we're looking for**
The formal name and existing literature for our architecture — a deterministic feature extractor followed by a probabilistic output layer — and an honest understanding of how our method compares to the state of the art in that family.

**The key concept we take from it: "Bayesian Last Layer" (BLL)**

This paper gives our architecture its proper name. The idea:

- A full **Bayesian Neural Network (BNN)** places probability distributions over *all* parameters and yields distributed predictions — but training and inference are **typically intractable**, so approximations must be used.
- A **Bayesian Last Layer (BLL)** network is a practical approximation: distributions are placed **only on the weights of the linear output layer**, while all earlier layers stay deterministic. This yields a normally distributed prediction.

**This is exactly our structure:**

| Their BLL | Our project |
|---|---|
| Deterministic feature extractor (earlier NN layers) | **Fixed random reservoir** |
| Bayesian linear output layer | **HMC-inferred readout** |

So our method sits squarely in the **Bayesian last layer / subspace-BNN family**. This is good — it means our approach is principled and recognised, not eccentric.

**Plain-language summary of the paper**

**The problem.** Standard neural networks are bad at knowing what they don't know. In safety-critical applications this is a real limitation, which is why uncertainty-aware methods like **Gaussian processes** and **Bayesian linear regression** are often preferred instead. Full BNNs would solve this, but they're intractable to train and infer.

**Their approach.** Use a BLL network: only the last layer is Bayesian. But this raises a question — how do you train all the *other* (deterministic) layers? The principled answer is to obtain point estimates for them by **maximising the marginal likelihood**. The catch: the marginal likelihood is **expensive to evaluate** in this setting, which is what has made this hard in practice.

**Their contributions — two:**

1. **A reformulation of the log-marginal likelihood** that allows the network to be trained efficiently with ordinary **backpropagation**. Key trick: they avoid a costly matrix inverse by *reintroducing* the last-layer weights (which had been marginalised out) as optimisation variables. They show this reformulation satisfies the conditions of optimality for the same solution as the original. The result is a simpler training procedure than variational inference.

2. **Better uncertainty on extrapolation points.** They provide a metric to quantify *how far outside the training data* a test point lies, relate this to the BLL covariance computation, and derive an algorithm that adjusts a scalar parameter to improve the log-predictive density on additional validation data.

**Their results.** Compared against **(a) Bayesian linear regression with fixed features** and **(b) a BNN trained with variational inference**, their method achieves the **highest log-predictive density on test data**.

**The uncomfortable part — and how we answer it**

**The problem:** their baseline "**Bayesian linear regression with fixed features**" *is basically our method*. A fixed feature map, followed by Bayesian regression on top. And they **beat it**.

We must not pretend otherwise. But there are three genuine, defensible differences:

**1. Their features are learned; ours are free.**
They *train* their feature extractor (by maximising marginal likelihood, via backpropagation). We do not train ours at all — the reservoir is random and fixed. Training features costs data and computation; we have neither to spare in the small-data regime. Our features are free.

**2. Their inference is approximate; ours is exact.**
BLL yields a *normally distributed* prediction — a closed-form Gaussian approximation. We use **HMC**, which samples the true posterior without assuming it is Gaussian. If the readout posterior is skewed or non-Gaussian, we capture that and they do not.

**3. Their features have no temporal memory; ours do.**
This paper is not about time series. A reservoir's recurrent state carries fading memory of recent inputs, which is essential for our task and absent from a standard feedforward feature extractor.

**The line to use:**
> "Fiedler and Lucia demonstrate that a *learned* feature extractor with a Bayesian last layer outperforms Bayesian linear regression on *fixed* features. This project deliberately accepts fixed features — the untrained reservoir — because it removes the need for feature-learning data entirely, which is the binding constraint in the small-data regime, and because the reservoir supplies temporal memory that a feedforward extractor does not. In exchange, exact posterior inference is performed by HMC rather than the Gaussian approximation their BLL formulation assumes."

**How it connects to what we did**

Our Week 4 model *is* a Bayesian last layer, with the reservoir as the (untrained) feature extractor:

```python
# S = reservoir states (the "fixed features" / deterministic feature extractor)
with pm.Model() as reservoir_hmc:
    w = pm.Normal('w', 0, 1, shape=S.shape[1])   # Bayesian LAST LAYER
    b = pm.Normal('b', 0, 1)
    sigma = pm.HalfNormal('sigma', 1)
    mu = pm.math.dot(S, w) + b                   # linear output layer
    obs = pm.Normal('obs', mu=mu, sigma=sigma, observed=target)
    idata = pm.sample(1000, tune=1000, chains=4) # EXACT inference (not Gaussian approx)
```

Every element maps to their framework — except that our feature extractor is untrained and our inference is exact.

**What we must add to our Limitations chapter because of this paper**

Be honest and pre-empt the criticism:

"A recognised limitation of Bayesian last layer methods is that uncertainty in the feature representation itself is not modelled — only the output layer carries a posterior. In this project the reservoir is random and fixed, so any uncertainty arising from the choice of reservoir is not quantified. Furthermore, Fiedler and Lucia show that *learning* the feature extractor by marginal-likelihood maximisation can improve predictive density over fixed features. Whether the reservoir's freedom from training outweighs this loss in the small-data regime is an empirical question, and a direct comparison is left to future work."

**Stating this ourselves earns marks. Being caught out by it loses them.**

**Why we cite it**

- The **primary citation** for the Bayesian Last Layer framework — the family our method belongs to (Chapter 2 and Chapter 3).
- The **honest benchmark** we must acknowledge in the Discussion.
- Its "extrapolation" metric is relevant to a question we should ask: does our uncertainty grow correctly when predicting *outside* the range of the training data?

**Suggested future-work line (genuinely strong):**

"A natural extension would be to compare the untrained reservoir directly against a marginal-likelihood-trained feature extractor within the same HMC readout, isolating the value of feature learning under a fixed inference scheme."
