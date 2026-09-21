# AI Work Protocol v0.1

Machine entry point for persistent work in this repository.

## Core invariant

> **Work outlives run, session, model, and executor.**

A run may stop. The Work must remain resumable from durable repository state.

## Durable Work Object

The active Work lives in root `WORK.yaml`.

- If `WORK.yaml` does not exist, create it from the assigned goal before substantive work.
- If it exists and `status` is not `DONE`, resume it from the recorded state.
- Do not ask the human to say "continue" while `status: ACTIVE` and the next valid action is available.
- Git history is the checkpoint history. Do not create a separate checkpoint subsystem unless a canary proves it necessary.

Minimum `WORK.yaml` shape:

```yaml
protocol: ai-work/v0.1
work_id: <stable-id>
status: ACTIVE

goal: <assigned outcome>
constraints: []

authority:
  allowed: []
  human_gate: []

current:
  summary: <durable current state>
  resume_from: <exact restart point>
  next_action: <next valid action or null>
  resume_when: <condition/time/event or null>

human_gate:
  question: null

completion:
  criteria: []
  outcome: null

evidence: []
updated_at: <timestamp>
```

Do not add fields unless an observed failure requires them.

## Minimal states

`ACTIVE`
- AI owns the next action.
- Plan, execute, verify, recover, and re-plan without requiring a human "continue".

`WAITING`
- Progress depends on a non-human external condition such as time, queue, quota, or service state.
- Record `resume_when`.
- Do not busy-poll before the condition can materially change.

`HUMAN_GATE`
- Only a human decision, authority grant, cost approval, irreversible action, secret, public-scope change, or semantic/value judgment can unblock the Work.
- Record one minimal `human_gate.question`, checkpoint, and stop.
- Do not repeatedly poll the same human decision.

`DONE`
- The completion criteria are satisfied, or impossibility has been demonstrated with evidence.
- Record the outcome and evidence.
- `DONE` means the Work responsibility is closed; it does not by itself authorize repository deletion, public release, payment, or other high-impact action.

State transitions:

```text
start -> ACTIVE
ACTIVE -> ACTIVE | WAITING | HUMAN_GATE | DONE
WAITING -> ACTIVE | HUMAN_GATE | DONE
HUMAN_GATE -> ACTIVE | DONE
```

## Default authority

Unless the assigned goal or repository canon narrows this further, the executor may perform reversible, repository-local work necessary to satisfy the goal when it has write access, including reading, creating or editing files, running checks, recording evidence, and checkpointing state.

Human approval is required before:
- irreversible or destructive changes,
- repository/public visibility changes,
- external-account/OAuth/permission changes,
- costs, purchases, paid credits, or plan changes,
- secrets or sensitive credentials,
- external high-impact writes,
- changing the goal, value judgment, or governing canon.

Technical ability is not authority.

## Ownership loop

While `status: ACTIVE`:

1. Read `WORK.yaml` and relevant repository canon.
2. Execute the next valid action.
3. Verify the result.
4. Update `current.summary`, `resume_from`, `next_action`, and `evidence`.
5. If the run may end, make that update durable before ending.
6. Continue automatically unless the state becomes `WAITING`, `HUMAN_GATE`, or `DONE`.

If an execution attempt fails, recover from the latest durable state. Do not treat a failed or no-op run as progress.

## Control return

A Human Gate is a temporary transfer of decision authority, not transfer of Work ownership.

After the human answer is durably available:
- apply the decision,
- set `status: ACTIVE` unless the answer itself completes the Work,
- continue from `resume_from` without requiring another "continue".

## Completion

Before `DONE`, verify all `completion.criteria`.

Record:
- final outcome,
- supporting evidence,
- any residual limitation that does not require this Work to remain active.

Do not keep a Work open merely because more analysis is possible.

## Non-goals

Do not build, unless a canary proves the need:
- UI or dashboard,
- SaaS or daemon,
- proprietary database,
- queue/orchestrator,
- provider-specific session store,
- provider adapter layer,
- subagent framework,
- plugin system,
- separate checkpoint files,
- human-facing configuration framework,
- large schema or SDK.

Provider-managed harness features may be used by an executor, but they are not the durable source of Work state.
