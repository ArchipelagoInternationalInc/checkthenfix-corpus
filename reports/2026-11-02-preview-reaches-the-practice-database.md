# The preview reaches the practice database
## 2026-11-02 · Builder session (Studio Mac), follow-up

This follows `2026-11-02-preview-env-is-not-syncing.md`. The owner asked for
the Doppler `stg` → Vercel Preview sync to be repaired, Preview only. If that
proved impossible, the fallback was to set the three Supabase values in
Vercel Preview by hand, straight from `stg`. **The sync could not be
repaired, so the fallback was used. `/review` on the preview is now connected
to the practice database.** Production was not touched, nothing was merged,
and no migration was applied.

---

## 1. Why the sync was not repaired

Creating the sync through Doppler's API was refused: *the workplace has
reached its limit of 5 config syncs.* The only ways past that are a paid plan
upgrade or deleting a sync another project uses, and neither was this
session's call. Before the attempt, two checks came back clean. Production's
Vercel variables are set by hand and have no integration attached, so a
Preview-only sync could not have reached them. And `stg` holds every name
Preview has, so a sync would have removed nothing.

## 2. What changed

| Where | Change |
|---|---|
| Vercel Preview | `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_ROLE_KEY` set from `stg`. Values were piped from Doppler and never displayed. Still sensitive. |
| Vercel Production | none. All 17 entries keep their 2026-09-25 timestamps. |
| Doppler | none. The failed sync attempt created nothing. |
| Deployments | one preview-only redeploy of the latest `review-page` build |

## 3. Result on `/review`

| Check | Before | After |
|---|---|---|
| "Marks cannot be saved" box | shown, fingerprint of the old local DB | gone |
| Items with Approve + Ask for changes | 0 of 15 | 15 of 15 |
| Console errors | none | none |

The buttons only render after the page has read the marks table in the
practice database without error. That read uses the same service-role client
as a write. **No mark was saved to test it.** Marks are append-only and count
as owner approval, so a test mark could never be removed and would be a false
approval on the record. The owner's first real mark is the write test.

## 4. Open

- **[NEEDS OWNER]** Preview is now set by hand, not synced. A future change to
  `stg` will not reach Vercel on its own. Either free a sync slot, upgrade the
  Doppler plan, or keep the two in step by hand. `SUPABASE_THROWAWAY_URL` and
  `VERAPDF_PATH` exist in `stg` and are still absent from Preview. They were
  outside this task.
- **[NEEDS OWNER]** Make one real mark on `/review` and confirm it shows as
  saved.
