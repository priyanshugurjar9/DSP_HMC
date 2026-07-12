### Skeldon & Dijk — "The complexity and commonness of the two-process model of sleep regulation from a mathematical perspective" (2025) — [Ref 14]

**Type:** Open-access article · *npj Biological Timing and Sleep*, vol. 2, article 24 · DOI: 10.1038/s44323-025-00039-z · Published 18 June 2025
**Code & data:** https://github.com/mas1as/Two_Process_Model

---

**What we're looking for**
Background on how sleep is modelled mathematically — the domain context for the actigraphy data our method may eventually be applied to. This paper explains *why* sleep-wake patterns look the way they do, which is what our reservoir will be learning to predict.

---

**Plain-language summary**

**The core idea — two processes.** Sleep timing is explained by two things interacting. First, **homeostatic sleep pressure** (called *process S*): a "tiredness" level that builds up while you're awake and drains away while you sleep. Second, the **circadian rhythm** (*process C*): your ~24-hour body clock. In the model, sleep pressure rises and falls between an **upper threshold** (hit it → you fall asleep) and a **lower threshold** (hit it → you wake up), and those thresholds are *wobbled up and down* by the circadian rhythm. That's the whole model — and it has been the standard framework in sleep science since Borbély's 1982 paper.

**It's real mathematics, not just a picture.** The paper stresses that the two-process model is a set of **differential equations** with **eight parameters**: two time constants (χ_w, χ_s — how fast sleep pressure rises during wake and falls during sleep), two asymptotes (μ_w, μ_s), two mean threshold levels (H₀⁺, H₀⁻), the circadian amplitude (a), and the circadian period. Typical human values quoted: χ_w = 18.2 h, χ_s = 4.2 h.

**The "natural period" result.** With the circadian rhythm switched off, the model becomes a simple oscillator, and the paper derives closed-form expressions for how long you'd sleep and stay awake. For standard parameters the natural sleep-wake cycle is ~22.6 h, with ~26% of it spent asleep.

**The surprising part — a simple model with rich behaviour.** The paper's main mathematical point is that this "simple" model is actually a **forced oscillator** (the sleep-wake cycle being pushed around by the circadian clock), and such systems have a deep, well-studied structure: **Arnold tongues** and a **Devil's staircase**. This structure explains a lot of real phenomena from one framework:
- **Monophasic sleep** (one sleep per day) — the biggest "tongue".
- **Polyphasic sleep** (several sleeps per day) — happens when the thresholds are close together, shortening the natural period.
- **Internal desynchrony** — what happens to people isolated from clocks and daylight: sleep and body-clock rhythms drift apart.
- **Bicircadian rhythms** — one sleep every *two* days.
- **Childhood transitions** — babies going from 3 sleeps → 2 → 1 per day follows the Devil's staircase pattern.

**The extension — light.** The original model can't explain what *keeps* the body clock locked to 24 hours. The paper describes extending it so that **light** entrains the circadian clock, which then entrains sleep — a hierarchical system, with feedback (because *when we sleep* determines *when we see light*, especially with electric light). This extended model can be personalised to individuals and used to predict the effect of light interventions.

**Why it matters clinically.** The extended model gives quantitative predictions for sleep phenotypes and for interventions to realign sleep — explicitly **including in people with neurodegenerative disorders**.

---

**How it connects to our project — and the important caveat**

**This is domain background, not our method.** The two-process model is a **mechanistic differential-equation model**: you write down equations for sleep pressure and fit their eight parameters. Our project is the opposite approach — a **data-driven** model (a reservoir computer) that learns the pattern from the time series without assuming any sleep equations.

So we cite this paper for **motivation and context**, not for method:
- It explains the *structure in the data* our reservoir will have to learn: a circadian rhythm with day/night activity, and disrupted rhythms in patients.
- It justifies why sleep actigraphy is a meaningful target — and why **disrupted rhythms in neurodegenerative disease** (our Parkinson's-style synthetic signal) are clinically important.
- It's the reference for terms we use loosely — circadian rhythm, sleep pressure, fragmented sleep.

**One genuine point of contact worth making in the Discussion:** the two-process model has **eight parameters fitted to data**, and the paper openly discusses uncertainty about which parameters explain which phenomena. That is *exactly* the kind of place a Bayesian approach with uncertainty quantification is valuable — a natural bridge between this literature and ours.

---

**Why we cite it**
As domain background for sleep/circadian rhythms and the clinical motivation for actigraphy in neurodegenerative disease. **Not** as a methodological source — our method is data-driven, not a mechanistic ODE model.
