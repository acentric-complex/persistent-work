# Persistent Work

**English** | [日本語](README.ja.md)

**Persistent Work** is a minimal, vendor-neutral protocol for AI work that must survive a single run, session, model, or executor.

> **Work outlives run, session, model, and executor.**

The durable source of work state belongs in the repository, not in a provider session.

## Why

AI executors can perform increasingly long, multi-step work, but execution can still stop because of session boundaries, model or executor changes, quotas, external waits, human approval gates, interruptions, or failures.

Persistent Work keeps the work resumable with a small durable work object and Git history as checkpoint history.

## Minimal model

The active work object is `WORK.yaml`, with four states:

```text
ACTIVE      AI owns the next action
WAITING     progress depends on a non-human external condition
HUMAN_GATE  only a human decision or authority grant can unblock the work
DONE        completion criteria are satisfied
```

A human "continue" should not be required while work is `ACTIVE` and a valid next action is available.

## Start here

- [AI_WORK.md](AI_WORK.md) — machine-first protocol
- [examples/ownership-canary/WORK.yaml](examples/ownership-canary/WORK.yaml) — minimal completed Work object
- [examples/ownership-canary/result.txt](examples/ownership-canary/result.txt) — example artifact

A fresh AI executor should be able to enter through this README, discover `AI_WORK.md`, initialize or resume `WORK.yaml`, execute, verify, checkpoint, and continue until `WAITING`, `HUMAN_GATE`, or `DONE`.

## Design principles

- **Durable state over session state**
- **AI owns execution while ACTIVE**
- **Human Gate is authority transfer, not work-ownership transfer**
- **External waiting is not a Human Gate**
- **Evidence before DONE**
- **Git history is the default checkpoint history**
- **Do not add infrastructure until a real failure proves the need**

## Non-goals

Persistent Work is not, by default, a SaaS platform, daemon, queue/orchestrator, proprietary database, provider-specific session layer, subagent framework, plugin system, or large SDK.

Provider-specific harnesses may execute the work. They are not the durable source of work state.

## Acentric Complex

Persistent Work is published by **Acentric Complex**.

**Local autonomy. Shared boundaries. Emergent intelligence.**

## License

MIT. See [LICENSE](LICENSE).
