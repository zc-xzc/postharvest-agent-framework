# Framework Overview

Detailed notes on the closed-loop intelligent system for postharvest fruit handling.

## 1. Physical inputs

Sensing and acquisition layer. Streams a fruit batch (or conveyor flow) together with synchronized sensor records.

- **RGB vision** — appearance, surface, geometry cues.
- **Weight** — mass estimation per object.
- **Robot status** — device state for downstream safety gating.

## 2. Quality inference

Multimodal state estimation produces a per-object quality state:

```
q = (g, d, m, u)
```

where `g` = grade, `d` = defect, `m` = mass, `u` = uncertainty. Outputs include a defect / grade prediction together with a confidence and uncertainty estimate, so downstream planning can act on low-confidence cases rather than silently committing.

## 3. Supervisory agent

Knowledge-guided task planning over a **retrieve → reason → propose** cycle:

- **Retrieve** — relevant knowledge and prior context.
- **Reason** — combine perception state with constraints and goals.
- **Propose** — emit a candidate task and tool calls.

The agent operates on a **slow supervisory timescale**. It proposes tasks and tool calls but **never issues low-level motor commands** — those are produced only by the safety-gated execution layer.

## 4. Safe manipulation

Validated robotic execution behind a **safety gate** enforcing force / speed / workspace limits. The execution layer performs real-time control (sort / route / divert) and writes a per-object execution log. This separation keeps the deliberative agent decoupled from real-time, safety-critical control.

## 5. Traceable output

Inspection and audit trail: each handled object receives an ID linked to its sensor evidence and action log, with a PASS / RECHECK / REJECT disposition and batch-linked provenance. The traceability layer is what makes the closed loop auditable.

## Feedback loops

- **Physical feedback** — vision / force / device state returned to perception.
- **Supervisory feedback** — faults / quality audits / recovery returned to planning.

## Validation design

**A. Dataset and evaluation protocol** — independent fruit batches, human-verified quality labels, train / validation / held-out test splits, and evaluation under cross-batch and lighting shifts.

**B. Controlled system comparisons** — B0 (rules + fixed grasp) → B1 (vision-guided sorting) → B2 (+ constrained planning) → B3 (+ agent + closed-loop audit), isolating each module's contribution.

**C. Primary outcomes** — quality (F1 / grade accuracy), manipulation (success / damage rate), efficiency (cycle time / labor), and reliability (recovery / audit completeness).

---

*Conceptual research design — no experimental results are asserted.*
