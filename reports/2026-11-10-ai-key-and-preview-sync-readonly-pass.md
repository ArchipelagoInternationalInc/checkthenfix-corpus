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
