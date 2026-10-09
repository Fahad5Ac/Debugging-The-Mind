# Live Team Challenge

A responsive two-screen scoreboard for a four-team mini-game competition.

- `/` — read-only public display with animated rankings and team totals.
- `/admin` — a conductor-only score control room with quick adjustments, custom additions/deductions, and direct score editing.

## Run locally

```bash
npm install
npm run dev
```

Without environment variables, the app runs in **preview mode**. Scores persist in that browser's local storage and sync across tabs on the same device, so the interface can be tested immediately.

## Connect Supabase

1. Create a Supabase project.
2. Run [`supabase/schema.sql`](./supabase/schema.sql) in the project's SQL Editor.
3. Copy `.env.example` to `.env.local`, then add the project's URL and **anon** key.
4. In Supabase Authentication, create or invite the conductor account.
5. Run the final `update public.profiles ...` command at the bottom of `schema.sql`, replacing the UUID with that conductor user's Auth UUID.
6. Restart the dev server.

The SQL schema enables public reads but does not grant direct client-side score writes. Score changes run through restricted RPC functions that verify the signed-in profile has the `conductor` role. The service role key is never used or exposed in the browser.

## Customization

Edit the initial seeded names and colors in `supabase/schema.sql`. The matching local-preview defaults and cinematic themes are in [`src/lib/teams.ts`](./src/lib/teams.ts). For an existing event, change the names and `accent` values directly in the `teams` table.

## Film imagery

The current design uses editorial/promotional stills for the cinematic team backgrounds. North 24 Kaatham and Manichitrathazhu are bundled in `public/team-images` for reliable display. For a public production event, replace any artwork with media you have permission to use.

If Supabase was already configured with the original placeholder teams, run [`supabase/migrate-cinematic-teams.sql`](./supabase/migrate-cinematic-teams.sql) in the SQL Editor to permanently rename the existing team records without losing their scores.

## Awards ceremony

The public screen now has three synchronized states: the live full-screen graph, the winner reveal, and the second-prize reveal. In the conductor panel, use **Finish · reveal winner**, then **Next · reveal 2nd prize**. When the ceremony is over, **Return to live graph** reopens scoring.

For an existing Supabase project, run [`supabase/enable-awards-ceremony.sql`](./supabase/enable-awards-ceremony.sql) in the SQL Editor once. It adds a protected Realtime state machine, so only conductor accounts can advance the ceremony and every display updates together.
