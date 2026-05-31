# AI Meme Generator

A full-stack web app that converts trending news into viral memes using AI. Phase 1 establishes the complete foundation — dark UI, REST API, and database — ready for Phase 2 AI integration.

## Run & Operate

- `pnpm --filter @workspace/ai-meme-generator run dev` — run the React frontend (port auto-assigned)
- `pnpm --filter @workspace/api-server run dev` — run the API server
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string (auto-provisioned)

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite, Tailwind CSS, Framer Motion, wouter (routing)
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — OpenAPI contract (source of truth)
- `lib/db/src/schema/memes.ts` — Drizzle memes table schema
- `artifacts/api-server/src/routes/` — Express route handlers (health, status, memes)
- `artifacts/ai-meme-generator/src/` — React frontend
  - `src/components/` — Navbar, MemeCard, SearchBar, BackendStatus, LoadingSpinner, Footer
  - `src/pages/` — Home, Generate, Trending
  - `src/layouts/` — AppLayout wrapper
  - `src/services/` — placeholder for Phase 2 AI integrations
  - `src/utils/` — utility helpers
- `lib/api-client-react/src/generated/` — generated React Query hooks

## Architecture decisions

- Contract-first: OpenAPI spec gates codegen, which gates the frontend — spec is the single source of truth
- Phase 1 uses in-database placeholder data; Phase 2 will plug in real News API + AI caption/image generation
- Meme generation route stores results to DB so they persist and can be shown in trending
- Frontend always runs in dark mode; purple (#8b5cf6) is the single accent color throughout
- No auth in Phase 1; designed so Clerk/Replit Auth can be dropped in later without restructuring

## Product

- **Home** — hero with animated title, backend status badge, search bar, trending memes grid
- **Generate** — enter a topic/keyword, hit generate, get a meme with caption and template info
- **Trending** — full grid of all memes sorted by views, with search and filter

## API Endpoints

- `GET /api/healthz` — health check
- `GET /api/status` — returns "AI Meme Generator Backend Running"
- `GET /api/memes` — list all memes (paginated)
- `GET /api/memes/trending` — top memes by views
- `POST /api/memes/generate` — generate a new meme `{ topic, template? }`
- `GET /api/memes/:id` — get meme by ID (also increments view count)

## Phase 2 Roadmap

- News API integration — fetch trending headlines as topics
- AI caption generation — GPT/Claude to write funny captions
- Meme image generation — overlay text on template images
- Likes/reactions endpoint
- Share to social media

## Gotchas

- Always restart the API Server workflow after adding new routes (it builds before running)
- Run `pnpm --filter @workspace/api-spec run codegen` after any OpenAPI spec change
- Frontend uses `wouter` for routing, NOT react-router-dom
- Do not use `console.log` in server code — use `req.log` or the `logger` singleton
