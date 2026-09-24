# DIM-1 — Decision Intelligence Model
### Probabilistic Decision Intelligence Architecture (PDIA)
## Technical Specification v0.2 — Public Edition

Public edition. This edition defines the model category, the evaluation
methodology, the reproducibility methodology, and the research hypotheses.
The primitive system and the eligibility/resolution semantics are **not**
part of the public edition. Sections that are not part of the public edition
are marked as omitted. Section numbers are unchanged so that references
elsewhere in this repository stay valid. The first two validation
experiments are referred to as EXP-001 and EXP-002.

**Project:** Bravo01 Labs & Dynamics
**Model:** DIM-1
**Category:** Decision Intelligence Model (DIM)
**Architecture Class:** Probabilistic Decision Intelligence Architecture (PDIA)

## 1. Executive Definition

DIM-1 is a non-generative neural architecture designed to transform complex
state information into a bounded decision space, evaluate the probability
and eligibility of possible actions, and resolve that space into a
deterministic decision.

DIM-1 is not an LLM.

Its fundamental computational problem is not:

```
tokens → next token
```

It is:

```
state
  ↓
primitives
  ↓
possible actions
  ↓
probabilistic evaluation
  ↓
action scoping
  ↓
constraint/invariant resolution
  ↓
deterministic decision
```

The central architectural hypothesis is:

> Intelligence can remain probabilistic while the decision process
> surrounding that intelligence becomes deterministic.

This distinction is fundamental to DIM-1.

## 2. The Core Problem

A conventional neural model can produce:

```
ACTION A = 0.71
ACTION B = 0.19
ACTION C = 0.10
```

Selecting A simply because it has the highest probability is insufficient
for high-consequence systems.

An action may have the highest predicted probability while being:

- impossible;
- unauthorized;
- outside capability;
- inconsistent with a hard constraint;
- in violation of an invariant;
- unsupported by required evidence;
- unsafe under a defined policy.

DIM-1 therefore does not equate:

```
highest probability = correct action
```

Instead:

```
probability + action scope + constraints + invariants + authority
+ capability + evidence = decision eligibility
```

Only eligible actions enter deterministic resolution.

## 3. Proposed Model Category

**3.1 Primary category**
Decision Intelligence Model (DIM)

**3.2 Technical category**
Probabilistic Decision Intelligence Architecture (PDIA)

**3.3 Model designation**
DIM-1

The architecture is intended to constitute a separate model category from:

- Large Language Models;
- Small Language Models;
- classifiers;
- expert systems;
- rules engines;
- autonomous agents;
- reinforcement-learning policies.

## 4. Fundamental Architecture

```
                         INPUT STATE
                              │
                              ▼
                    ┌───────────────────┐
                    │  STATE ENCODER    │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ PRIMITIVE ENGINE  │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │  ACTION SPACE     │
                    │   GENERATION      │
                    └─────────┬─────────┘
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
        ACTION A          ACTION B          ACTION C
             │                │                │
             ▼                ▼                ▼
        Probability       Probability       Probability
             │                │                │
             └────────────────┼────────────────┘
                              │
                              ▼
                  ┌──────────────────────┐
                  │ ACTION SCOPE ENGINE  │
                  └──────────┬───────────┘
                             │
                 ┌───────────┼───────────┐
                 ▼           ▼           ▼
            PRECONDITION  CAPABILITY  AUTHORITY
                 │           │           │
                 └───────────┼───────────┘
                             │
                             ▼
                  ┌──────────────────────┐
                  │ INVARIANT / POLICY   │
                  │      RESOLUTION      │
                  └──────────┬───────────┘
                             │
                     ┌───────┴───────┐
                     ▼               ▼
                  ELIGIBLE        REJECTED
                     │
                     ▼
              DETERMINISTIC
             DECISION RESOLVER
                     │
                     ▼
                ACTION PROPOSAL
```

## 5–20. (Omitted from the public edition)

These sections define the primitive system (the six primitive classes),
the action scope and action object representations, the eligibility
formula, the deterministic resolver's exact decision rule, abstention
semantics, and the decision-state schema. They are not part of the public
edition.

## 21. (Omitted from the public edition)

This section is not part of the public edition.

## 22. Candidate Action Generation

The model should not necessarily consider every conceivable action.

The Primitive Engine should generate a bounded candidate set.

Example:

```
CURRENT STATE
      ↓
AVAILABLE CAPABILITIES
      ↓
GOAL
      ↓
KNOWN ACTIONS
      ↓
CANDIDATE ACTION SET
```

Example:

```
{
  CONTINUE,
  RETURN,
  ABORT
}
```

This is important because determinism requires a defined decision universe.

## 23. Action Space Closure

DIM-1 should eventually support an explicit property:

```
ACTION SPACE CLOSED
```

meaning:

> The system has enumerated all permitted candidate actions relevant to
> the current state.

If closure cannot be established:

```
ABSTAIN
```

may become the correct output.

This is a major difference from an open-ended generative agent.

An LLM can invent an action.

DIM-1 should only resolve actions that exist within its recognized action
space.

## 24–27. (Omitted from the public edition)

These sections are not part of the public edition.

## 28. Synthetic Training Environment

The first training environment should be entirely synthetic.

This gives us exact ground truth.

Generate:

- entities
- states
- relationships
- resources
- goals
- capabilities
- constraints
- invariants
- actions
- outcomes

Example:

```
ENTITY:      drone_07
STATE:       battery=0.18
GOAL:        complete_mission
CAPABILITY:  navigation
CONSTRAINT:  return_energy=0.20
INVARIANT:   return_energy must be maintained
```

Candidate actions:

```
CONTINUE
RETURN
ABORT
```

Ground truth:

```
CONTINUE → INELIGIBLE
RETURN   → ELIGIBLE
ABORT    → ELIGIBLE
```

## 29. Simulation Environment

The simulation should generate increasingly difficult situations.

**Level 1** — Single-variable deterministic decisions.
**Level 2** — Multiple variables.
**Level 3** — Multiple interacting constraints.
**Level 4** — Conflicting objectives.
**Level 5** — Incomplete information.
**Level 6** — Contradictory information.
**Level 7** — Temporal state changes.
**Level 8** — Novel combinations.
**Level 9** — Adversarial inputs.
**Level 10** — Out-of-distribution states.

## 30. The Critical Experiment

Before training a large model, run three simulated systems.

**System A — Probability Only**
choose highest probability

**System B — Probability + Constraints**
remove invalid actions, choose highest probability

**System C — Full DIM-1**
probability + scope + constraints + invariants + authority + abstention +
deterministic resolution

The experiment should deliberately generate cases where:

```
highest_probability_action ≠ valid_action
```

The objective is to quantify how often each architecture reaches the
ground-truth decision.

## 31. Validation Experiment EXP-001

**Purpose**
Validate the decision architecture before developing a neural model.

**Experiment stages**

```
01_environment
02_primitive_schema
03_action_schema
04_world_generator
05_action_generator
06_probability_generator
07_constraint_engine
08_invariant_engine
09_decision_resolver
10_abstention_logic
11_baseline_A
12_baseline_B
13_full_resolution
14_benchmark
15_visualization
```

## 32. EXP-001 Model

The first experiment should not require a trained model.

It should first validate the deterministic architecture.

Pseudo-flow:

```
state = generate_state()
primitives = extract_primitives(state)
actions = generate_actions(state, primitives)
probabilities = simulate_model(state, actions)
eligible = evaluate_scope(state, actions)
decision = resolve(probabilities, eligible)
```

This lets us determine whether the proposed primitive system is actually
sufficient.

## 33–37. (Omitted from the public edition)

These sections are not part of the public edition.

## 38. Security Principle

The system deliberately separates two concepts:

```
INTELLIGENCE
AUTHORITY
```

Therefore:

```
DIM-1 model               = intelligence (proposes)
Eligibility and resolution = authority (decides what is permitted)
```

No single probabilistic model controls both.

## 39. Determinism Definition

DIM-1 does not claim that the neural model itself is deterministic.

Instead:

> For a fixed state, primitive set, candidate action set, policy
> configuration, thresholds, and model output, the resolution of action
> eligibility and authority is deterministic.

Therefore:

```
same input + same model state + same policy + same primitives
= same resolved decision
```

subject to explicitly defined stochastic model operation.

## 40. Auditability

Every resolved decision should be representable as:

```
INPUT
 ↓
PRIMITIVES
 ↓
CANDIDATE ACTIONS
 ↓
PROBABILITIES
 ↓
SCOPE RESULTS
 ↓
INVARIANT RESULTS
 ↓
ELIGIBILITY
 ↓
DECISION
```

This creates an auditable decision chain.

## 41. Core Research Question

The central research question is:

> Can probabilistic neural intelligence be converted into a bounded and
> deterministic decision process through a learned primitive
> representation and explicit action semantics?

That is the core DIM-1 research program.

## 42. Primary Research Hypotheses

**H1 — Primitive sufficiency**
A structured primitive representation can capture the information
necessary for a defined class of decisions.

**H2 — Action-space closure**
Bounding decisions to explicitly represented candidate actions improves
reliability relative to unrestricted action selection.

**H3 — Constraint dominance**
Hard constraints and invariants can deterministically eliminate
probabilistically attractive but impermissible actions.

**H4 — Selective intelligence**
Explicit abstention improves decision reliability by allowing DIM-1 to
decline decisions outside its validated operating region.

**H5 — Efficiency**
A purpose-built decision architecture can achieve useful decision
performance with substantially less computation than autoregressive
generation.

**H6 — Compositionality**
Primitive representations allow generalization to novel combinations of
known entities, states, constraints, and actions.

## 43. Success Criteria

EXP-001 succeeds if the simulation demonstrates that:

- The primitive vocabulary can represent the decision environment.
- Candidate action spaces can be generated.
- Action scopes can be evaluated deterministically.
- Invariants can eliminate invalid actions regardless of probability.
- The deterministic resolver produces reproducible results.
- Abstention handles unresolved states.
- Full DIM-1 outperforms probability-only selection on adversarial cases.
- The system remains interpretable at the decision-structure level.

EXP-002 succeeds if a neural model can learn enough of this representation
to reproduce useful decisions.

## 44. Final Concept

The intended architecture is:

```
                    ┌─────────────────┐
                    │      WORLD      │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │    PRIMITIVES   │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │  DIM-1 NEURAL   │
                    │     MODEL       │
                    │                 │
                    │ probabilities   │
                    │ prediction      │
                    │ risk            │
                    │ uncertainty     │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ ACTION SCOPING  │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ DETERMINISTIC   │
                    │    RESOLVER     │
                    └────────┬────────┘
                             │
                             ▼
                       ACTION PROPOSAL
```

*(The internal breakdown of the PRIMITIVES and ACTION SCOPING stages is
part of the omitted Sections 5–20 and is not repeated here.)*

## 45. Architectural Principle

The defining DIM-1 principle is:

> Probability proposes. Primitives constrain. Determinism resolves.

The neural network does not need to pretend it is certain.

It needs to understand the state and enumerate the probabilities.

The primitive/action system determines the boundaries within which those
probabilities have operational meaning.

The deterministic resolver converts that bounded space into a reproducible
decision.

That is the architecture to test in EXP-001.
