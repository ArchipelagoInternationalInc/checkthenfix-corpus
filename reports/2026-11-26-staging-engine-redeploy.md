# Test engine updated; one of two file checks done
## 2026-11-26 · Studio Mac session

The owner approved this on Nov 25: update the **test** scanning engine only.
The live engine was not touched.

## What was done

| Step | Result |
|---|---|
| Commit deployed | `7c5a9e0` (tip of `builder/formal-wording`, Nov 25) |
| Environment | Test (`staging`), checked twice before deploying; named explicitly on the deploy command too |
| Deploy | 02:50–02:52 UTC Nov 27 (9:50 PM ET Nov 26). Build succeeded, deployment online |
| Engine health | OK. Validator veraPDF 1.30.2, pinned 1.30 |
| Engine address | Read from the secrets store by name, never printed; it matches the test environment |

## The two file checks (on the preview site)

| File | Expected | Result |
|---|---|---|
| 01-simple-text (a short page of real text) | "Real text, not scanned images" reads Yes; score no longer 38 | **PASS.** Reads Yes; score 50 |
| 06-password-open (a password-protected file) | Page says the file is password-protected | **NOT RUN.** The upload was refused by this session's permission check; waiting for the owner |

Test files are the rebuilt Nov 19 break-it set (made-up words only), as
committed in the app repository.

## Rows created

One upload, anonymous, on the preview site, which points at the practice
database. Nothing was charged. It deletes itself after the 72-hour window.

## Findings for a builder (not fixed here)

1. **The homepage sample score no longer matches.** The canonical sample
   (May 5 agenda) now scores **50** with the current checker, but the
   homepage's sample card and the verification script still expect **42**.
   It is likely the Nov 17 change that stopped treating short real text as a
   scan. Changing a displayed number needs the owner's eye, so it is left as is.
2. **Six remediation tests fail** at `7c5a9e0` (in-place tagging and the
   image-decision rule). This is unrelated to the engine redeploy.
3. Verification also reports an author-identity mismatch, the deployed pages
   behind the preview protection wall, and no mail records for the domain.
   None of these comes from this deploy. Identity is a stop for the owner,
   not something to work around.
