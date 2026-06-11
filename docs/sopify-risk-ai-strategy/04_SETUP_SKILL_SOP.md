# Setup Guide — sopify-sdlc SKILL (SOP)

**Asana:** [Docs SKILL SOP Setup](https://app.asana.com/1/1201761273978832/project/1214825140218161/task/1215605704341618)

How to install, trigger, and maintain the `sopify-sdlc` skill in Claude Cowork / Claude Code.

## What the skill is

A single self-contained playbook covering the whole GS Battery SDLC: Requirements (MoSCoW, stories, AC, DoR/DoD) → Design (brand guideline, Figma Make) → Database (PostgreSQL standards, migrations, RLS) → Development (Code Standard SOP-DEV-001) → Testing (Vitest/Supertest/k6, two gates) → Git workflow → Deploy (Railway/AWS). It also ships `ref/` folders (00_ONBOARDING … 08_AWS) with the detailed Thai-language SOP documents.

## Install

**Cowork:** Settings → Capabilities → Skills → add/upload the `sopify-sdlc` skill (as a `.skill` package or via the team plugin). Confirm it appears in the skills list.

**Claude Code:** place the skill folder at `~/.claude/skills/sopify-sdlc/` (personal) or `<repo>/.claude/skills/sopify-sdlc/` (project-scoped, recommended so the whole team gets it via git). Folder must contain `SKILL.md` at its root.

## Trigger

- Type **`/sop`** to enter the working procedure (single hub, non-linear phases).
- It also auto-triggers when a request matches its description (SDLC work on GS Battery products).
- Verify install: ask Claude "which skills are available?" — `sopify-sdlc` must be listed.

## How it runs (operating model)

1. User starts with `/sop`; phases are Brainstorm → Design → Backend → Database → Test Cases → Push, enterable in any order.
2. Each phase output is final only when its quality gate passes; **Push is blocked until every phase is complete.**
3. Database phase needs the Supabase MCP; Push needs the GitHub Connection (see `03_SETUP_SUPABASE_GITHUB_PLUGINS.md`).
4. After first Railway link by IT, the loop is: edit in Cowork → PR to `main` → auto-deploy.

## Maintenance & versioning

- The skill is versioned (currently `1.0.0`). Changes to the SOP go through the same git workflow: branch → PR → review → bump `version` in the frontmatter.
- Keep `SKILL.md` (binding rules) and `ref/` (detailed guides) in sync — if a gate changes in the refs, change it in SKILL.md in the same PR.
- Installed skill copies in Cowork sessions are read-only; edit the source repo and re-distribute via Settings → Capabilities.
- Re-validate after each change against `01_SKILL_VALIDATION_REPORT.md` checks (frontmatter, gate consistency, phase coverage).

## Checklist

- [ ] Skill listed in Cowork / `.claude/skills/`
- [ ] `/sop` responds with the phase hub
- [ ] Supabase MCP + GitHub Connection set up (doc 03)
- [ ] Team knows: gates are binding — work skipping validation, tests, or pushing to main gets rejected
