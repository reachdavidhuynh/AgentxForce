# AgentForce — Grok Build rules

Multi-agent graph orchestrator. Live: https://agentxforce.com  
Repo: reachdavidhuynh/AgentxForce

## Stack
- Next.js 15.5 App Router, React 19, TypeScript, Tailwind
- `@xyflow/react` for the 2D canvas
- Vercel AI SDK (`ai`, `@ai-sdk/xai`, `@ai-sdk/openai`, `@ai-sdk/anthropic`)
- NextAuth v5 — **Google OAuth only**
- Deploy target: Vercel → agentxforce.com

## Commands
```bash
npm install
npm run dev      # local
npm run build    # must pass before claiming a change done
```

## Architecture (keep intact unless product asks otherwise)
| Area | Location | Notes |
|------|----------|--------|
| Auth | `auth.ts`, `app/api/auth/[...nextauth]` | Google only. JWT sessions. |
| Middleware | `middleware.ts` | Protects `/portal` + `/dashboard`. Logged-in `/login` → `/portal`. |
| Portal | `app/portal/page.tsx` | First-login templates. Sets onboard flag → `/dashboard?project=<seed>`. |
| Templates | `lib/projects.ts` | blank, research, startup, partnership, finance |
| Seeds | `lib/seed-graph.ts` | `buildProjectSeed(seed)` → `{ nodes, edges, task }` |
| Dashboard | `app/dashboard/page.tsx`, `AgentNode.tsx` | React Flow, multi-edge, companies/teams, Ext interfaces |
| Orchestrate | `app/api/orchestrate/route.ts` | Topo-sort, cross-company external-brief, URL fetch, webhooks |
| Token router | `lib/token-router.ts` | control plane → user BYOK → env |

## Graph rules
- One node may fan-out to many targets (multiple edges allowed).
- Cross-company links only when **both** nodes have `exposed: true` (External interface).
- Cross-company edges: dashed amber, label `external`; orchestrate sanitizes as external brief.
- Companies: Acme Corp, Nova Labs, Orbit Systems (color-coded).
- Prefer extending `ROLE_PRESETS` / `COMPANIES` over parallel systems.

## UI conventions
- Dark zinc palette (`#0a0a0a` background). Title “AgentForce” prominent but understated.
- Client components start with `'use client'`.
- BYOK keys in `localStorage` key `agentforce_user_keys_v1` only — never log or commit them.

## Known gap (high priority)
Dashboard on `main` does **not** yet read `?project=` and call `buildProjectSeed`.  
Wire with `useSearchParams` + `Suspense` + seed effect so portal templates actually populate the mesh. Until then the portal navigates correctly but the canvas defaults to a single researcher.

## Environment (Vercel)
**Required for auth:** `AUTH_SECRET`, `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`  
**Optional fallbacks:** `XAI_API_KEY`, `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`  
**Future control plane:** `TOKEN_CONTROL_URL`, `TOKEN_CONTROL_SECRET`  
Google redirect URI: `https://agentxforce.com/api/auth/callback/google` (and localhost for dev).

## Do not
- Re-add email / magic-link auth unless explicitly requested.
- Hardcode API keys or put them in client bundles.
- Drop multi-edge or Ext cross-company checks without a clear product reason.
- Loud marketing on the landing page.

## Verification before claiming done
1. `npm run build` succeeds.
2. If auth/portal touched: unauthenticated → `/login`; after Google → `/portal`; project pick → `/dashboard?project=…`.
3. If graph/orchestrate touched: fan-out edges work; cross-company only between Ext nodes; run produces log + outcome.
