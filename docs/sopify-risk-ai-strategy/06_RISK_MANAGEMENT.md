# Risk Management — Sopify & AI-Assisted SDLC

**Asana:** [Docs : Risking](https://app.asana.com/1/1201761273978832/project/1214825140218161/task/1215605704341632)

Risk methodology and register for running the Sopify working procedure (Non-Dev users building apps with AI in Cowork) — aligned with the CISO Risk Management Platform workstream.

## Methodology

Score each risk **Likelihood (1–5) × Impact (1–5)**. Level: Low 1–6, Medium 8–12, High 15–25. Each risk gets an owner, a mitigation, and a review cadence. Treatments: mitigate, accept (with sign-off), transfer, avoid. Register reviewed at each phase gate and monthly by the project owner; High risks escalate to CISO review.

## Risk register

| ID | Risk | Phase | L | I | Level | Mitigation (already in SOP unless noted) | Owner |
|---|---|---|---|---|---|---|---|
| R-01 | Secrets leaked: token/`service_role` key committed or shared insecurely | DB/Push | 3 | 5 | **High** | Env-only secrets, `.env` gitignored, IT token handoff via secure channel, secret scanning on repo (add: GitHub secret scanning + push protection) | IT |
| R-02 | AI-generated SQL injection / unsafe code merged | Dev | 2 | 5 | Medium | Parameterized queries only, Zod everywhere, senior review MUST-blocks, OWASP scan Gate 2 | Senior Dev |
| R-03 | Non-Dev user skips a quality gate and ships | All | 3 | 4 | **High** | Push blocked until all phase gates pass; branch protection; CI required | Burased |
| R-04 | Schema changed after data lands (no approval step followed) | DB | 3 | 4 | **High** | Schema doc approval before SQL; migrations with tested UP/DOWN | DB owner |
| R-05 | RLS missing/misconfigured → tenant data exposure | DB | 2 | 5 | Medium | RLS required on multi-tenant tables; Supabase advisors check before go-live | IT |
| R-06 | Prod deploy with reused dev JWT secret | Deploy | 2 | 5 | Medium | Fresh `openssl rand -base64 32` per environment; pre-deploy checklist item | IT |
| R-07 | Railway auto-deploy ships a broken `main` | Deploy | 3 | 3 | Medium | PR + CI gate before `main`; health check + smoke tests post-deploy; rollback = revert commit | IT |
| R-08 | AI hallucinates requirements/AC → wrong product built | Req | 3 | 4 | **High** | Mandatory user approval of requirements doc; AC drive UAT; Gate 2 UAT sign-off | PM |
| R-09 | Untested AI code: coverage targets unmet, tests weakened to pass | Test | 3 | 4 | **High** | CI-enforced Gate 1 (coverage ≥70%); reviewer checks assertions; never-ignore-failing-test rule | Senior Dev |
| R-10 | Single-person dependency (IT for Supabase/Railway setup) | Ops | 3 | 3 | Medium | Document runbooks (docs 03–04); add: second trained IT admin | IT |
| R-11 | Vendor/model dependency: Cowork/Claude or Codex behavior change breaks procedure | Strategy | 2 | 3 | Low | Short/Mid/Long-term strategy (Codex Sandbox → Claude Code Enterprise → Sopify both); skill versioning | Burased |
| R-12 | Unreviewed AI dependency additions (supply chain) | Dev | 2 | 4 | Medium | Dependencies Checklist (4.1.1) tiers + security checklist; lockfile in PR review | Senior Dev |
| R-13 | Data residency/PDPA breach via cloud services | All | 2 | 5 | Medium | Add: data-classification rule before any real data enters Supabase; prod data only after CISO sign-off | CISO |
| R-14 | Cost runaway (Railway/AWS/Supabase) | Ops | 2 | 2 | Low | Budget alerts (Cost Explorer / Railway limits) per SOP | IT |

## Gate-embedded risk controls

Risk review is not a separate ceremony — it rides the existing gates: Phase 1 gate confirms NFR/security requirements captured (R-08, R-13); Phase 3 gate confirms RLS + constraints (R-04, R-05); Phase 4 gate confirms validation + no secrets (R-01, R-02); Testing Gate 2 confirms OWASP 0-critical + UAT (R-02, R-09); go-live gate confirms secrets, WAF, backups, monitoring (R-06, R-07).

## Escalation

High risk materializes → stop the affected phase, notify project owner (Burased) same day; secret exposure additionally → rotate keys immediately (Supabase + JWT), audit access logs, notify CISO. Track incidents as Asana tasks in this project.
