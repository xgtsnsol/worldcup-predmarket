<!-- BEGIN:nextjs-agent-rules -->
# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` before writing any code. Heed deprecation notices.
<!-- END:nextjs-agent-rules -->

<!-- BEGIN:deploy-rules -->
# Vercel Deployment

This project is linked to Vercel project **worldcup-hackathon** (`prj_yFXg8jqEt2W0C0w45lE7i0XoZ9Bc`).

## Deploy command
```bash
bun run deploy
# or directly: vercel --prod --yes
```

## Production URLs
- Primary: https://worldcup-hackathon.vercel.app
- Preview: https://worldcup-hackathon-mnoi5vii6-erick-carvajal-s-projects.vercel.app
- Vercel dashboard: https://vercel.com/erick-carvajal-s-projects/worldcup-hackathon

## Env vars required (set in Vercel dashboard)
Check `.env.example` for the full list. Key ones:
- `NEXT_PUBLIC_SOLANA_RPC` — Solana RPC endpoint
- `NEXT_PUBLIC_TXLINE_API_URL` — TxLINE API base URL
- `TXLINE_JWT` — TxLINE JWT for keeper bot
- `TXLINE_API_TOKEN` — TxLINE API token for keeper bot
- `NEXT_PUBLIC_SUPABASE_URL` + `NEXT_PUBLIC_SUPABASE_ANON_KEY` — Supabase
- `PAYER_SECRET_KEY` — keeper bot payer keypair (JSON array of numbers)
- `KEEPER_SECRET` — optional bearer token to authenticate manual keeper triggers

## Keeper Bot (auto-settlement)
The keeper bot runs as a Vercel Cron Job every 5 minutes:
- **Route**: `POST /api/keeper/settle` (or `GET` with `x-vercel-cron: 1`)
- **Config**: `vercel.json` — `crons` array with schedule `*/5 * * * *`
- **Logic**: `src/lib/keeper.ts` — fetches all Active escrows, resolves fixtureId by name via TxLINE, checks if finished (StatusId 5/10/13), gets scores + validation proofs, calls `settle_with_cpi`
- **Auth**: Cron requests include `x-vercel-cron: 1` header; manual requests need `Authorization: Bearer ${KEEPER_SECRET}`

### Manual trigger
```bash
curl -X POST https://worldcup-hackathon.vercel.app/api/keeper/settle \
  -H "Authorization: Bearer <KEEPER_SECRET>"
```

## Build flow
1. `git add -A && git commit -m "..."` (if changes)
2. `bun run deploy` — auto-detects Next.js, builds with Turbopack, deploys

## Known issues
- `eslint` key in `next.config.ts` is not supported in Next.js 16 — use `.eslintrc` instead.
- Solana wallet adapter packages need `transpilePackages` in config.
- The Anchor IDL in `src/idl/settlement.json` must match the deployed program.
<!-- END:deploy-rules -->
