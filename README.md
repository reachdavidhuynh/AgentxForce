# AgentForce

Multi-agent graph orchestration with per-agent model choice, BYOK API keys, and token routing.

**Live:** [agentxforce.com](https://agentxforce.com)

## Features

- **Multi-provider agents** — xAI (Grok), OpenAI, Anthropic per node
- **2D graph** — drag-and-drop nodes, fan-in / fan-out connections
- **Companies & teams** — multi-org graphs with controlled external interfaces
- **Roles** — Researcher, Coder, Financial Analyst, CEO, Head of Product, and more
- **BYOK** — paste your API keys in the dashboard (browser storage)
- **Token router** — control plane → user keys → server env
- **Auth** — Google OAuth only (NextAuth)
- **Portal** — first-login project templates seed the mesh
- **Connectors** — URL fetch, Slack webhook, generic webhook (Zapier)

## Flow

1. Land on agentxforce.com → **Continue with Google**
2. After OAuth → **Portal** (pick a starter project on first visit)
3. Open mesh at `/dashboard?project=…` with pre-configured companies, teams, and links

## Grok Build

This repo includes an `AGENTS.md` so [Grok Build](https://x.ai/build) loads project rules automatically.  
Open the repo root and run `grok` (or `grok inspect` to verify rules discovery).

## Setup

```bash
npm install
npm run dev
```

### Environment variables

| Variable | Required | Purpose |
|----------|----------|----------|
| `AUTH_SECRET` | Yes (auth) | NextAuth secret |
| `GOOGLE_CLIENT_ID` | Yes (Google) | OAuth client ID |
| `GOOGLE_CLIENT_SECRET` | Yes (Google) | OAuth client secret |
| `XAI_API_KEY` | Optional | Server fallback for Grok |
| `OPENAI_API_KEY` | Optional | Server fallback |
| `ANTHROPIC_API_KEY` | Optional | Server fallback |
| `TOKEN_CONTROL_URL` | Optional | Future central key vault |
| `TOKEN_CONTROL_SECRET` | Optional | Auth for control plane |

Google OAuth redirect URI: `https://agentxforce.com/api/auth/callback/google` (and `http://localhost:3000/api/auth/callback/google` for local).

## Deploy

Vercel: connect this repo, set env vars, deploy.

**Domain:** [agentxforce.com](https://agentxforce.com)

## License

Private / all rights reserved unless stated otherwise.
