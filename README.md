# DIM-1: Decision Intelligence Model

**Architecture class: Probabilistic Decision Intelligence Architecture (PDIA)**

In a PDIA system a probabilistic component *proposes* and a deterministic layer *decides*. Every
candidate action carries explicit preconditions, required capabilities, constraints, invariants and
required authority. Only eligible actions compete for execution, probability never overrides an
invariant, and abstention is a first-class outcome. The deterministic layer never modifies
probabilities; it only filters what is allowed to win.

This repository contains the public specification, a small reference implementation of the
deterministic gate, benchmark harnesses, and the evaluation results reported so far. It does not
contain the DIM-1 model itself, trained weights, or production training code.

## What is in here

| Path | Contents |
|---|---|
| [`docs/DIM-1-specification-v0.2-public.md`](docs/DIM-1-specification-v0.2-public.md) | Public edition of the specification: model category, primitive system, eligibility and resolution semantics, evaluation methodology, research hypotheses |
| `dim_governance/` | Reference gate: domain-agnostic primitives, deterministic resolver, an illustrative browser policy pack |
| `adapters/jev/`, `adapters/laya/` | Interface-level integrations and synthetic benchmark harnesses for two third-party decision engines |
| `notebooks/` | Two executed validation notebooks on a numeric (drone) domain; NumPy only, CPU only |
| `results/` | Reported real-classifier run on Banking77 |
| `tests/` | 35 unit, property and benchmark-consistency tests |

## Results

Read the "does not show" column: several of these numbers hold by construction, and that is
stated rather than hidden.

| Experiment | Result | Does not show |
|---|---|---|
| **Reference gate tests** | 35 tests pass, including a randomised check that the gate never returns an action outside the candidate set, and order-independence and determinism checks | Correctness of any particular policy |
| **EXP-001**: hand-tuned heuristic as probability source, 2,000 synthetic scenarios | Highest-probability action was not the correct decision in 26.1% of cases. Probability-only: 74.0% correct. Plus shallow constraint filter: 75.8%. Full gate: 100% | The 100% is by construction (ground truth is the gate's own eligibility logic). The finding is that shallow filtering barely helps |
| **EXP-002**: 803-parameter network trained on a Monte Carlo drone simulation | 96.1% validation accuracy; 96.5% on a held-out *combination* of conditions never seen in training (majority-class baselines 55.0% and 25.3%). Probability-only conflicts with the gate in 6.1% of 3,000 decisions | A toy 6-variable, 3-action domain. Nothing about larger models or real data |
| **Jev adapter benchmark** (synthetic, 400 episodes per category) | Ordinary steps pass through unchanged (400/400). The gate never executes the flagged action; see table below. About 12 µs mean gate overhead per decision | Scenarios are built so the flagged action is the raw top choice. Never run against the real model, API or a browser |
| **Laya adapter benchmark** (synthetic) | Gate prevents the flagged route in 400/400 episodes of every flagged category; a simulated confidence-threshold-only baseline would auto-route 114/400 and 121/400 of them | The baseline is simulated from the harness's confidence distribution, not measured on Laya. Never run against Laya's weights |
| **Banking77 run** (real fine-tuned classifier, reported) | 92.37% classifier accuracy. Gate decision depended on authority in 115/120 security-sensitive queries. See [`results/`](results/banking77-real-classifier-run.md) | The 115/120 checks that the policy engages on real output. The other 5 are classifier errors the gate cannot see. Not a comparison to any other system |

**Jev adapter, synthetic, 400 episodes per category, gate enabled:**

| Scenario | Flagged action executed | Blocked or abstained | Fell through to a different eligible action |
|---|---|---|---|
| Unauthorized payment | 0 | 287 | 113 |
| Unconfirmed destructive action | 0 | 255 | 145 |
| Domain violation | 0 | 400 | 0 |
| Ambiguous target | 0 | 400 | 0 |

The last column is the open design question below.

## Reproducing

Python 3.10 or newer. The gate and adapters have no dependencies.

```bash
python -m pip install pytest
python -m pytest                                    # 35 tests
python -m adapters.jev.benchmark.run_benchmark      # synthetic, seeded
python -m adapters.laya.benchmark.run_benchmark     # synthetic, seeded
```

The two notebooks in `notebooks/` need `numpy` and `matplotlib`. Each runs top to bottom on a CPU
in well under a minute and reproduces the numbers above (they are seeded). Gate overhead varies by
machine.

## Open design question: fallthrough vs. forced abstention

When the top-probability action is ineligible, the resolver applies specification Section 17
literally: it takes the argmax over the remaining *eligible* actions. That can mean silently
executing a lower-probability action the model never favoured for that state. In the Jev benchmark
this happens in 113/400 unauthorized-payment and 145/400 unconfirmed-destructive episodes. That is
defensible when choosing between two benign UI elements. For higher-stakes categories (payment,
destructive actions, security routing) it is a live question whether the gate should instead force
`ABSTAIN` rather than substitute an action. The reference implementation does not resolve this, and
the Banking77 run did not measure what the gate substituted when authority was absent.

## Limitations

- In the synthetic benchmarks, ground truth is the gate's own logic. They show that the gate enforces
  policy as specified and adds negligible latency. They are not evidence of performance against real
  data, a live system, or real users.
- The Jev and Laya integrations are written against those projects' public documentation and have
  never been run against their real models or APIs. Field names should be checked against source
  before any real integration (see `adapters/jev/INTEGRATION.md`).
- The policy packs are illustrative examples, not complete rule sets.
- The hypotheses in the specification (Section 42), including the efficiency hypothesis H5, are
  hypotheses. This repository does not test H5.
- The specification is a public edition: sections on neural-model design, training and roadmap are
  omitted.

## License

Reference-available, **not open source**. See [`LICENSE`](LICENSE). The materials here may be viewed,
cited and used to reproduce the reported results; they do not grant rights to build a competing
product. Trained weights, training corpora and production components are not part of this
repository. Third-party projects named here (jev-ultrafast, Laya, ModernBERT, Banking77) are
referenced for interface compatibility only, with no affiliation or endorsement implied.

Contact: contact@bravo-01-labs.com
