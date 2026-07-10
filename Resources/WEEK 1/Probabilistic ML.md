### Murphy — *Probabilistic Machine Learning: Advanced Topics* (2023)

**Type:** Book, MIT Press · Relevant material: chapters on MCMC and Hamiltonian Monte Carlo (*verify chapter/page in your copy*)

**What we're looking for**
A more mathematical treatment of the same ideas, to back up the intuition from McElreath with precise definitions of priors, posteriors, and samplers.

**Plain-language summary**
This is a graduate reference text. Where McElreath teaches by example, Murphy gives the formal statements: what a posterior distribution is, why sampling is needed, and how Monte Carlo methods approximate expectations we cannot compute directly. It presents HMC as a modern, efficient member of the MCMC family and situates it against older methods such as random-walk Metropolis.

**Which method we took from it**
Nothing new operationally — we use it as the rigorous reference for the Bayesian framework that underlies the readout in our project. It justifies, in formal terms, why returning a posterior distribution over the readout weights is meaningful.

**How it connects to what we did**
When we later place priors on the reservoir's readout weights (Week 4), this text is the citation for treating that readout as a proper Bayesian model rather than an ad-hoc trick.
