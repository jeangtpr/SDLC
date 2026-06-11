# Proposed Addition — Multiagent Operating Model for sopify-sdlc

**Asana:** [Add Multiagent in SKILLS](https://app.asana.com/1/1201761273978832/project/1214825140218161/task/1215605704341622)

> Installed skills are read-only inside Cowork sessions, so this is a ready-to-paste section. To apply: edit the skill source, insert the section below into `SKILL.md` (after "Operating Model"), bump version to **1.1.0**, and re-distribute via Settings → Capabilities.

---

## Multiagent Operating Model (paste into SKILL.md)

```markdown
## Multiagent Operating Model — parallelizing phases safely

When running in an environment with subagents (Cowork / Claude Code Agent tool), split work
across specialized agents. The orchestrator (main session) owns all gates — subagents never
self-approve.

### Agent roles

| Agent | Phase(s) | Mandate | Hard limits |
|---|---|---|---|
| Requirements Analyst | 1 | MoSCoW, user stories, AC, NFRs | Read-only on code; output is a doc for user approval |
| UI Designer | 2 | Figma Make prompts, screen specs, brand-guideline conformance | Must reuse brand tokens; no new tokens |
| DB Engineer | 3 | Schema doc → (after approval) migrations UP/DOWN, RLS | No SQL before the schema doc is approved |
| Backend Developer | 4 | MVC scaffold, Zod, services, repositories, error classes | No `any`; logic in services; tests alongside |
| Frontend Developer | 4 | Components, hooks, API integration, async states | Tailwind only; no inline styles |
| Test Engineer | 6 | Vitest/Supertest suites, coverage report, k6 scripts | Never weakens an assertion to pass; reports failures |
| Reviewer (senior) | 7 | PR review with MUST/SHOULD/NIT/LEARN labels | Independent agent — never the author agent |

### Orchestration rules

1. **Gates stay with the orchestrator.** A subagent reports "done"; only the orchestrator
   runs the phase quality gate and the user grants approvals (requirements, schema).
2. **Parallelize only independent work.** Allowed: UI Designer ∥ DB Engineer after
   requirements approval; Backend ∥ Frontend after schema approval; Test Engineer
   alongside development. Never parallelize a phase with its own upstream artifact.
3. **Contract-first handoffs.** Backend ↔ Frontend share the API spec (endpoint, method,
   request/response in the standard shape) before either starts; DB Engineer publishes
   the approved schema doc as the single source for both.
4. **Author ≠ reviewer.** The Reviewer agent must not be the agent that wrote the code.
   MUST comments block merge exactly as in the human flow.
5. **One branch per agent task**, named `<type>/<asana-id>-<desc>`; the orchestrator
   merges via PR — subagents never push to main/develop.
6. **State file.** Orchestrator keeps `SOPIFY_STATE.md` in the repo root: per-phase
   status (pending/in-progress/gate-passed), artifact links, open blockers. Subagents
   read it on start and append results — it is the cross-agent memory.

### DO NOT (multiagent)

- Let a subagent approve its own gate or skip the user-approval checkpoints.
- Run DB migrations from two agents concurrently.
- Spawn agents for trivial single-file edits — overhead beats benefit.
```

---

## Rationale

The SOP's gate discipline maps cleanly onto an orchestrator/worker pattern: gates already define handoff points, and DoR/DoD already define what a "task" given to an agent must contain. The two real risks of multiagent work — racing on shared state and self-approval — are addressed by rules 1, 2 and 6 (single gate owner, dependency-aware parallelism, shared state file).
