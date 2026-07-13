### Jaeger — "The 'echo state' approach to analysing and training recurrent neural networks" (2001)

**Type:** Technical Report 148, German National Research Center for Information Technology (GMD), Bonn, Germany
**Where the method sits:** the echo state property and the fixed-reservoir/trained-readout design are set out in the early sections (*verify page in your copy*)

---

**What we're looking for**
The original definition of an echo state network — the exact model we are looking built — and the reason the recurrent weights can be left untrained.

---

**Plain-language summary**

This report introduced the **echo state network**, and its central claim is genuinely surprising: **you do not need to train the recurrent connections of a recurrent neural network at all.**

Instead, you:
- Create a large pool of neurons wired together with **fixed random weights** (the "reservoir")
- Drive it with your input signal
- Let it produce a rich, high-dimensional **nonlinear response**
- Train **only** a single linear readout that reads that response — by ordinary linear regression

For this to work, the reservoir must have the **echo state property**: the influence of past inputs must gradually **fade** rather than grow, so the network's state depends on recent history in a stable way. If old inputs never faded, the reservoir would be dominated by its distant past and be useless; if they faded instantly, it would have no memory at all.

In practice the echo state property is achieved by scaling the recurrent weight matrix so that its **spectral radius** (its largest absolute eigenvalue) is **below about 1**.

---

**Which method we took from it**

The entire reservoir construction:
1. Fixed random input weights and recurrent weights
2. Scaling the recurrent matrix by its spectral radius, for stability
3. Driving the reservoir with the signal to collect its internal states (the features)
4. Training **only** the readout, by least squares

---

**How it connects to what we did**

Our Week 3 code is a direct, minimal echo state network — 100 nodes, random fixed weights, spectral radius 0.9, `tanh` state update, readout by least squares. This is precisely the recipe in the report:

```python
N = 100
W_in  = np.random.randn(N,1)*0.5     # fixed random input weights
W_res = np.random.randn(N,N)*0.1     # fixed random recurrent weights

eigs  = np.max(np.abs(np.linalg.eigvals(W_res)))
W_res = W_res/eigs*0.9               # spectral radius -> 0.9 (echo state property)

for i in range(len(X)):              # drive the reservoir, collect states
    x = np.tanh(W_in@u + W_res@x)

W_out,*_ = lstsq(states, Y)          # train ONLY the readout
```

Every line above maps to something in this paper: the fixed random weights, the spectral-radius scaling that guarantees the echo state property, the `tanh` state update, and the fact that `W_out` is the *only* thing learned.

---

**Why we cite it**

It is the **primary citation** for the reservoir / echo state network in Chapter 3. If we cite one paper for our method's first half, this is it.
