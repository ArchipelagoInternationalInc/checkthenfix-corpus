# The preview's database settings are not syncing
## 2026-11-02 · Builder session (Studio Mac)

The task was to point preview builds at the practice database so `/review`
can save marks (branch `review-page`, draft PR 4, owner-approved Nov 2). The
planned fix was to copy the Supabase settings from Doppler `dev` into `stg`.
That fix turned out to be unnecessary: `stg` was already correct. The real
fault is further down the line, and **the red "Marks cannot be saved" box is
still there.** Production was not touched, nothing was merged, and no
migration was applied.

---

## 1. What was checked

All comparisons used fingerprints. No values were compared in plain text.

| Check | Result |
|---|---|
| `dev` URL host vs practice fingerprint `070eddddd526607e` | match |
| `stg` URL host vs practice fingerprint | **already a match** |
| `dev` vs `stg`: URL, anon key, service role key, throwaway URL | all four identical, none empty |
| Latest `review-page` preview, after a fresh preview-only redeploy | `/review` still reports fingerprint `12ca17b49af22894` |
| `12ca17b49af22894` | the hash of the old local database address |

## 2. Finding

Vercel's Preview environment variables are written by the Doppler
integration. **The integration last wrote them on 2026-09-20.** Preview also
has no `SUPABASE_THROWAWAY_URL`, even though `stg` defines one. So `stg`'s
current values have not reached Vercel since September. Doppler shows the
Vercel integration as enabled, but its API lists no sync under it from this
machine.

A rebuild therefore cannot fix the preview. Every preview build gets the
September values, whatever `stg` contains.

## 3. What this session changed

- Doppler: **nothing.** The copy from `dev` to `stg` would have written values
  identical to what was already there, so it was skipped.
- Vercel: one preview redeploy of the latest `review-page` build. Production
  was not redeployed.
- Code: none.

## 4. Process note

While fingerprinting, one check echoed a raw configuration value to the local
terminal, because the redirect order in the command was wrong. The value was
the public (browser-shipped) project URL, not a key, and it went nowhere
except this machine. Every later check hashed the value inside a single
pipeline.

## 5. Open, and needs the owner

- **[NEEDS OWNER]** Repair or recreate the Doppler → Vercel sync from `stg` to
  the checkthenfix Preview environment, in the Doppler dashboard. This changes
  standing integration config, so it was not done here. The other option is
  to set the Preview variables in Vercel by hand, but that would fight the
  integration that owns them.
- After the sync runs, redeploy the latest `review-page` preview and recheck
  `/review`.
