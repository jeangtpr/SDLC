# Setup Guide — Supabase & GitHub Plugins (Claude Cowork)

**Asana:** [Docs Supabases, Github Plugins Setup](https://app.asana.com/1/1201761273978832/project/1214825140218161/task/1215605704341616)

Both connectors are required before a Sopify project can pass the Database and Push phases. Per the SOP operating model: IT owns the Supabase side; the user owns their own GitHub login.

## 1. Supabase MCP Server

**Who does what:** IT creates the Supabase project and connects the MCP server; the user receives the token via a secure channel (never chat/email in plain text).

1. IT creates the Supabase project (one per app) and collects: Project URL, `anon` key, `service_role` key.
2. In Cowork: **Settings → Connectors → Add connector → Supabase MCP**, authenticate with the access token IT provides.
3. Verify the connection by asking Claude to `list tables` on the project — an empty list is a correct result for a new project.
4. Store keys in the project's `.env` (and `.env.example` without values). **Never commit them.** The `service_role` key must never reach the frontend or the repo.

**Used in:** Phase 3 (Database) — schema doc approval first, then Claude applies migrations through the MCP (`apply_migration`), checks `get_advisors` for security warnings, and enables RLS on multi-tenant tables.

## 2. GitHub Connection

**Who does what:** the user creates and logs into their own GitHub account — credentials are never entered on anyone's behalf.

1. Create a personal GitHub account (if new) and ask IT to add it to the **SiamGS-Sopify** org: `https://github.com/SiamGS-Sopify`.
2. In Cowork: **Settings → Connectors → Add connector → GitHub**, complete the OAuth login yourself.
3. Verify by asking Claude to list repositories in `SiamGS-Sopify`.
4. Branch protection (IT, once per repo): `main` and `develop` protected — PR required, CI green, 1 approval on `develop`, 1–2 on `main`, no force-push.

**Used in:** Push phase — code goes to the repo through the connection (no zip handoff). Every PR links its Asana task.

## 3. Railway link (one-time, IT)

After the first push, IT links the repo to Railway: service + env vars (Supabase URL/keys, fresh prod JWT secret via `openssl rand -base64 32`), build/start commands, health check `GET /api/health`. From then on: edit in Cowork → push to `main` via PR → Railway auto-deploys.

## Verification checklist

- [ ] Supabase MCP responds to `list_tables`
- [ ] `.env` holds keys; `.env.example` committed without values; `.env` in `.gitignore`
- [ ] GitHub connector lists `SiamGS-Sopify` repos
- [ ] Branch protection active on `main`/`develop`
- [ ] Railway health check returns `{ "status": "ok" }` after first deploy

## Troubleshooting

| Symptom | Fix |
|---|---|
| Supabase MCP "unauthorized" | Token expired/wrong project — request a re-issued token from IT |
| Push rejected on `main`/`develop` | Working as intended — open a PR from a `feature/*` branch |
| Railway build fails but local passes | Check `typescript` is in `dependencies`, and `build`/`start` scripts exist |
| RLS blocks all reads after enabling | Policies missing — write per-role policies before enabling RLS in prod |
