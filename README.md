# NHL Tracker
 
Live scores, schedules, standings and player stats for all 32 NHL teams.
Built on the NHL's public web API (`api-web.nhle.com/v1`) — no key required.
 
## Setup
 
```bash
npx create-next-app@latest nhl-tracker --ts --app --eslint --no-tailwind --src-dir=false --import-alias "@/*"
cd nhl-tracker
```
 
Then drop these files in, replacing what the generator made:
 
```
lib/nhl.ts
lib/teams.ts
app/globals.css
app/layout.tsx
app/page.tsx
app/team/[code]/page.tsx
app/team/[code]/LiveGame.tsx
app/api/live/[gameId]/route.ts
```
 
Delete `app/page.module.css` — nothing imports it.
 
```bash
npm run dev
```
 
Open http://localhost:3000 — it redirects to `/team/MTL`.
 
## How it works
 
**Server components fetch the NHL API directly.** The API sends no CORS
headers, so the browser can't call it. Next's server components are the
proxy layer — no extra backend needed for the static parts.
 
**Caching is per-endpoint.** Schedules cache for an hour, standings for
ten minutes, the scoreboard for fifteen seconds. That's the `revalidate`
argument in `lib/nhl.ts`.
 
**Live games poll `/api/live/[gameId]`.** `LiveGame.tsx` only starts the
interval when the game state is `LIVE` or `CRIT`, and clears it on
unmount. The route sets `s-maxage=10`, so a hundred viewers still produce
roughly one upstream call every ten seconds.
 
## Deploy
 
```bash
git init && git add -A && git commit -m "NHL tracker"
gh repo create nhl-tracker --public --source=. --push
```
 
Import the repo at vercel.com. No environment variables needed yet.
 
## Next
 
- Auth (Auth.js) + Postgres so users can save favourite teams and players
- Nightly stat snapshots, so you can chart trends the API won't give you
- Player detail pages via `/v1/player/{id}/landing`