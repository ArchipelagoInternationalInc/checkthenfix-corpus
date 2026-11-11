# AI key for the engine, and the preview sync: read-only pass

Date: 2026-11-10. Studio Mac. Branch `builder/dod-loop`.
Owner-approved task (Nov 6): give the engine its AI key, and repair the
Doppler sync for the preview environment. **Nothing was changed in any
platform this session.** No secret value was printed.

## What is now known

**Job 1: the AI key**

- Doppler `stg` holds both `JUDGMENT_API_KEY` and `JUDGMENT_MODEL` (by name,
  21 names in all).
- Doppler has a Railway integration of type `sync`. That makes a Doppler
  sync the likely way the engine gets its variables, but it is **not yet
  confirmed**: the Railway CLI is signed out on this Mac, so the engine
  service's variable names could not be listed.

**Job 2: the preview sync**

- Doppler `stg` **already points at the practice database.** All four
  Supabase settings (`NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`,
  `SUPABASE_SERVICE_ROLE_KEY`, `SUPABASE_THROWAWAY_URL`) resolve to the
  practice project. This was compared by project reference only: the URL
  match, and the `ref` claim inside each key. The two keys carry the roles
  `anon` and `service_role` respectively. So the planned "set the three
  values in stg" step looks like a no-op.
- The workplace has **two** integrations: one Railway, one Vercel. Each can
  carry several syncs, and Doppler's API does not list syncs, so the five
  slots and their last-run times are still unread. That needs the dashboard,
  which asked for a sign-in.
- The workplace has 8 Doppler projects. The two named as deleted or on hold
  (the deleted one, and the one on hold) are not among them. Any sync still
  tied to them is orphaned and is the candidate for removal, still to be
  confirmed on the dashboard.

## Board

`verify.sh` checks 1 to 11 PASS. Check 12 went red because this session's
permission approvals added two `doppler` entries to the untracked local allow
list, which bypassed a guarded command. Both entries were removed.

## Open

- [NEEDS OWNER] Sign in to the Doppler dashboard, and run `railway login`.
- After that: list the syncs, confirm how the engine gets its variables, then
  put each change to the owner before making it.
- The paid-fix test, case (a), has not been run. The step file it names was
  not found on this Mac.

## Update, later the same day (owner signed in; owner approved the change)

**Job 1 is already done; nothing to change.** The engine service has
`JUDGMENT_API_KEY` and `JUDGMENT_MODEL`, delivered by the Doppler `stg` to
Railway sync, and both match `stg` by fingerprint. The engine health check is
ok, and its validator version matches the pin.

**Finding: the engine reads the live database, not practice.** Its Supabase
URL and both keys belong to the live project, while `stg` points at practice.
The sync still reports "In Sync". This is the September decision that let
public uploads work, but it means a preview-site fix will probably be refused
by the engine. **[NEEDS OWNER]** decide how one engine serves both. Nothing
was changed. Also seen: a second Railway project with no variables of its own,
which looks like a leftover.

**Sync slots: 5 of 5 were in use.** Railway (`stg`), and on Vercel: checkthenfix
`stg` to Preview (disabled), daat-institute `prd`, sendmylicense `prd`, and
steelticket `prd` (disabled).

**Changed (owner approved):** removed the sendmylicense `prd` to Vercel sync,
choosing "leave the secrets in Vercel". 4 of 5 slots are now in use.

**Re-enabling the checkthenfix `stg` to Preview sync failed.** It was enabled,
then Doppler disabled it again within the same minute. Doppler's reason:
Vercel will not accept one of the secrets as the Sensitive type. The fix means
changing the sync's variable type or a secret's visibility. **[NEEDS OWNER]**
Vercel Preview keeps its hand-entered values, which point at practice. The
/review check was not run, because nothing reached Vercel.

**Cause found (read-only).** Vercel holds the three `NEXT_PUBLIC_*` variables
without the "secret" visibility, because they are public build-time values.
The failed re-enable updated the Preview variables in alphabetical order, from
the `BREVO_*` names through `JUDGMENT_*`, all from `stg`, and stopped at the
first `NEXT_PUBLIC_*`. `STRIPE_WEBHOOK_SECRET`, `SUPABASE_SERVICE_ROLE_KEY`
and the public three keep their earlier values. The fix: mark the three
`NEXT_PUBLIC_*` values Unmasked in `stg`, and recreate the sync with the
Dynamic type, so masked values stay Sensitive. This step was blocked by the
session's safety check on writes to the secret store, and it is left to the
owner. Nothing in `stg` was changed.
