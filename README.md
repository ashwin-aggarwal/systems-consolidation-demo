# systems-consolidation-demo
Exploring regulated vs. unregulated memory consolidation — a reimplementation of Sun et al. (2023).

# When to Stop Studying: Exploring Systems Consolidation

An exploration of a mechanism from Sun, Advani, Spruston, Saxe & Fitzgerald (2023), *["Organizing memories for generalization in complementary learning systems"](https://www.nature.com/articles/s41593-023-01382-9)*, Nature Neuroscience.

## What this is

The paper proposes a model of how the brain decides which memories to consolidate from the hippocampus into the neocortex. It uses a **teacher–student–notebook** framework:

- **Teacher** — generates examples from a fixed underlying rule, with a controllable amount of noise (signal-to-noise ratio, or SNR)
- **Notebook** — stores examples exactly, standing in for fast hippocampal memory
- **Student** — learns slowly from replayed notebook examples, standing in for the neocortex

The central question: if the student consolidates from the notebook without limit, does it eventually start fitting noise instead of the real underlying pattern? And does gating consolidation with a validation check (early stopping) prevent that?

I built this from the paper's description rather than running the authors' own code, so that I'd understand the mechanism directly rather than just reproducing a figure. The notebook here uses a simplified exact-storage memory rather than the paper's Hopfield network implementation (see *Simplifications* below).

## Why I built this

I came across this paper and found the central idea, that memory consolidation should be regulated rather than unlimited, genuinely interesting. Rather than just reading it, I wanted to implement the mechanism myself from the paper's description to make sure I actually understood it. That process ended up surfacing a finding beyond the paper's headline result (see below).

## Results

Headline result (consistent with the paper's central claim): with a mostly-noisy teacher (SNR = 0.2), a student trained without any stopping criterion overfits, its error on held-out data climbs steadily the longer it trains, because it starts fitting to noise specific to the stored examples rather than the real pattern. A student that stops training once a small held-out validation set shows no further improvement stays flat instead. This is a much smaller-scale test than the paper's own experiments, but the qualitative pattern holds.

Additional finding (validation set size trade-off): I tested whether increasing the validation set's size straightforwardly improves things. It doesn't monotonically. Larger validation sets do make the stopping decision more consistent across runs (lower variance in when training stops), but they also leave less data for the student to actually train on. Averaged over 10 random seeds, the best final performance came from a middle-sized validation split, not the largest one, a concrete example of the classic ML trade-off between reliable model selection and available training data.


## Simplifications vs. the original paper

To keep the project scoped and focused on the core mechanism, a few things are simplified relative to the full paper:

- **Notebook**: implemented as exact pattern storage rather than a sparse Hopfield network with Hebbian one-shot encoding
- **Student/Teacher**: linear models rather than the paper's higher-dimensional configurations
- **Single consolidation mechanism**: only validation-gated early stopping is tested, not the paper's other regulation approaches

These are noted in the notebook itself at the point where each simplification is made.

## What I'd explore next

- Vary notebook capacity directly to confirm the validation-size trade-off is really about reduced training data
- Replace exact storage with an actual Hopfield network for a more biologically faithful notebook
- Test a nonlinear teacher (e.g. `tanh`-transformed outputs) that the student can't perfectly represent
## Reference

Sun, W., Advani, M., Spruston, N., Saxe, A., & Fitzgerald, J. E. (2023). Organizing memories for generalization in complementary learning systems. *Nature Neuroscience*, 26(8), 1438–1448. https://doi.org/10.1038/s41593-023-01382-9

---

Built by Ashwin Aggarwal — [aka96@cornell.edu | https://www.linkedin.com/in/ashwin-aggarwal/]
