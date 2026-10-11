# 
# Random recurrent networks learn memory tasks fastest just inside the ordered regime, with implications for relearning after visual deprivation

[![MATLAB](https://img.shields.io/badge/MATLAB-R2020a%2B-orange.svg)](https://www.mathworks.com/products/matlab.html)
[![GNU Octave](https://img.shields.io/badge/GNU%20Octave-8.x-blue.svg)](https://octave.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Code and data for the manuscript

> **Random recurrent networks learn memory tasks fastest just inside the ordered regime, with implications for relearning after visual deprivation**
> Mehdi Borjkhani, Hadi Borjkhani, Morteza A. Sharif

The code reproduces every figure and every number in the paper. The saved results are included, so the figures and statistics can be regenerated in a few minutes.

## Main result

We trained random tanh recurrent networks to recall their input from *k* steps earlier. The networks were initialised at different gains *g*, which set the largest Lyapunov exponent λ, and we counted how many training steps they needed to learn the task.

- **Where learning is fastest.** It is fastest on the ordered side (λ < 0), both when only a readout is trained and when every connection is trained by backpropagation through time.
- **At the edge of chaos (λ ≈ 0).** Networks need 2–3 times as many steps for short delays and fail on longer ones.
- **Chaotic networks** learn slowly or not at all.
- **Why it is fastest there.** During training, networks converge to a similar, task-dependent exponent (λ ≈ −0.15 for delay 20, λ ≈ −0.27 for delay 10), and learning is fastest for networks that start close to it. This is an association, not a causal test. Networks near the edge learn slowly while moving to a more ordered exponent, and their gradient norms rarely reach the clipping threshold.
- **Readout learning.** Near the edge, a trained readout is both less accurate and slower to approach its own optimum.
- **Effect of delay length.** The sweet spot moves toward the transition as the delay grows, from λ\* ≈ −0.30 (k = 5, 10) to −0.17 (k = 20), and does not move further at k = 30.
  - A rule fitted to k ≤ 20, λ\* = −2.6/k, predicted λ\* = −0.087 for k = 30.
  - The observed optimum was −0.175 (95% CI −0.221 to −0.135).
  - The prediction and its time stamp are in `results/prediction_k30.txt`.

### Visual cortex after blindness (Experiment F2, Fig. 6; untrained networks: Experiment F, Fig. S1)

- **Protocol.** Networks first learn the task. Their input amplitude then drops by 75%, and homeostatic scaling of the recurrent weights restores their activity level. Together these move the dynamics to the edge: median λ goes from −0.26 to +0.005.
- **Relearning.** After input is restored, through either the original code or a new prosthetic code, relearning with the scaling kept is slower than with it reversed. For the prosthetic code, across 8 networks, it takes 2.35× longer (95% CI 1.8–2.8).
- **Matched starting error.** Relearning remains slower when timed from the point where the error has fallen to the reversed network's starting error. This compares trajectories; it does not use matched starting states.
- **Frozen input weights** give the same result.
- **Signal strength.** Weak prosthetic signals are learned faster than strong ones.

## Quick start

In MATLAB or GNU Octave, from the repository folder:

```matlab
make_all_figures      % Figs 1-6, S1 and all numbers in the text (a few minutes)
run_all_experiments   % rerun all simulations (resumable; ~4 h on two cores)
```

## Contents

| Figure | Script | Experiment |
|---|---|---|
| 1 Regimes, Lyapunov exponents, error-signal reach | `figures_code/fig1_concept.m` | A, B, C |
| 2 Whole-network learning speed, sweet spot vs delay | `figures_code/fig2_bptt.m` | B, E |
| 3 Dynamics and gradients during training | `figures_code/fig3_training.m` | G |
| 4 Readout learning: achievable accuracy, speed, memory, dimensionality | `figures_code/fig4_readout.m` | A, A2 |
| 5 Robustness (size, learning rate, nonlinear task) | `figures_code/fig5_robustness.m` | B, D |
| 6 Relearning after deprivation in trained networks | `figures_code/fig6_vision.m` | F2 |
| S1 (Fig. 7, appendix, in the journal version) The same protocol in untrained networks | `figures_code/figS1_vision_untrained.m` | F |

| Experiment | Script | What it does |
|---|---|---|
| A | `experiments/expA_reservoir.m` | 200-unit fixed networks: exponents, memory profile, online (NLMS) readout learning time, participation ratio |
| B | `experiments/expB_bptt.m` | 64-unit networks trained by BPTT (Adam): delays 5, 10, 20; 12 gains × 8 networks |
| C | `experiments/expC_gradients.m` | error-signal propagation through the Jacobians; example traces |
| D | `experiments/expD_robustness.m` | N = 32/128, learning rates 1e-3/1e-2, product task |
| E | `experiments/expE_prediction.m` | delay 5 at fine resolution; out-of-sample test at delay 30 |
| A2 | `experiments/expA2_feasibility.m` | best achievable readout NMSE and samples to 90% of it |
| F | `experiments/expF_vision.m` | untrained networks: deprivation → homeostatic scaling (`src/homeo_theta.m`) → learning a prosthetic code |
| F2 | `experiments/expF2_pretrained.m` | trained networks: deprivation → scaling → relearning (same or new code, scaling kept or reversed, inputs trainable or frozen) |
| G | `experiments/expG_diagnostics.m` | Experiment B repeated with gradient norms, λ during training and an independent test set |

`figures_code/predict_k30.m` writes the delay-30 prediction from the delay 5–20 results only. `figures_code/stats_for_text.m` prints every number in the text; its output is in `results/stats_output.txt`.

- **`src/`** contains the network (`make_net`, `run_net`), BPTT training (`train_bptt`), the Lyapunov exponent (`lyap_rnn`) and seeding (`set_seed`), which works the same in MATLAB and Octave.
- **`results/`** contains the saved results. The per-run files (`expB/`, `expD/`–`expG/`, `expF2/`, one file per training run) are packed in `results/results_runs.zip`; `make_all_figures` unpacks it automatically on first use (or unzip it into `results/` by hand).

## Requirements

MATLAB R2020a or later (no toolboxes) or GNU Octave 8.x. The saved results were produced with GNU Octave 8.4.

## License

MIT. See [LICENSE](LICENSE).
