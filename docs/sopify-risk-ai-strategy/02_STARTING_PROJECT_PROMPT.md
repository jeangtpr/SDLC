# Starting Project Prompt — sopify-sdlc

**Asana:** [Starting Project Prompt](https://app.asana.com/1/1201761273978832/project/1214825140218161/task/1215605704341614)

Copy the prompt below into Claude Cowork (or Claude Code) to kick off a new project under the GS Battery SOP. Fill in the `<placeholders>` first.

---

## The prompt

```text
/sop

Start a new GS Battery project under the sopify-sdlc SOP.

Project name: <project-name>            (resource prefix: p-<project-name>)
One-line goal: <what the app does and for whom>
Primary users/roles: <e.g. admin, staff, viewer>
Deploy target: Railway (default) | AWS
Asana project: <link>
GitHub org/repo: https://github.com/SiamGS-Sopify/<repo-name>

Begin with Phase 1 — Requirements:
1. Interview me for the raw requirement list, then apply MoSCoW (Must/Should/Could/Won't).
2. Write user stories per role ("As a <role>, I want <goal>, so that <benefit>").
3. Write at least one testable acceptance criterion per story.
4. State non-functional requirements (auth model, rate limits, performance targets, integrations).
5. Stop and ask for my approval of the requirements document before any design,
   schema, or code — per the SOP, downstream work on unapproved artifacts is rejected.

Rules of engagement for the whole project:
- Follow every quality gate in sopify-sdlc; never skip a phase gate.
- Database: schema doc in markdown first → my approval → migrations (UP/DOWN).
- Code: TypeScript strict (no any), MVC layering, Zod on every request,
  standard {success, data|message} response shape, parameterized SQL only.
- Design: GS Battery brand guideline (blue-600 primary, IBM Plex Sans, mobile-first).
- Git: branch from develop as <type>/<asana-id>-<desc>, Conventional Commits,
  PR linked to Asana — never push to main/develop directly.
- Tests alongside code: Vitest unit (FE ≥70% / BE ≥80%), Supertest on 100% of endpoints.
- Secrets only in env vars; never commit .env; backend exposes GET /api/health.

Confirm the setup checklist before Phase 1:
Supabase MCP connected (token from IT), GitHub Connection authorized,
Asana project linked.
```

---

## Why each block exists

The header pins identity (naming, repo, Asana) so every later artifact links correctly. The Phase 1 block forces the SOP's first gate — requirements approval — before anything downstream. Rules of engagement compress the binding SOP rules Claude must hold for the entire session. The setup checklist catches missing connections (Supabase MCP, GitHub) before work starts instead of mid-phase.

## Per-phase follow-up prompts

After requirements approval, drive each phase with these one-liners (the skill keeps state; order is flexible but gates are not):

- Design: `Proceed to Phase 2 — produce the Figma Make prompt using the brand guideline, then the screen list with loading/empty/error states.`
- Database: `Proceed to Phase 3 — draft the schema document in markdown for my approval. No SQL yet.`
- Development: `Schema approved. Proceed to Phase 4 — scaffold backend (MVC + Zod + error classes) per endpoint with tests alongside.`
- Testing: `Run Phase 6 Gate 1 checks and show me the coverage report; list what Gate 2 still needs.`
- Push/Deploy: `All gates green — push to GitHub via the connection and run the pre-deploy checklist for Railway.`
