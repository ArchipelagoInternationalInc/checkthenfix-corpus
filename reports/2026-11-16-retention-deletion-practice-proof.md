# 72-hour deletion proven once on the practice database (2026-11-16)

Practice database only. The live database was never touched, and nothing was
merged, deployed or reconfigured. The app branch was builder/formal-wording at
4cd43e7.

| Check | Result |
|---|---|
| Staging config points at the practice database | PASS |
| Before: anonymous documents past 72 h | 12 documents, 19 stored files |
| One public sample PDF checked on the preview | PASS: report shown, upload accepted under the 50 MB PDF-only limit |
| Owner approved making that one test document expire now | 1 row changed |
| Deletion run | 13 rows and 20 stored files deleted, 0 failures |
| Test document, its check result and its stored file gone | PASS |
| Test report link in the browser | PASS: shows "not here" |
| Older expired documents gone; unexpired ones untouched | PASS: 0 of 12 left; 3 of 3 unexpired documents and 4 of 4 files kept |
| Hourly schedule for the deletion route; its secret exists by name in production config | PASS (value not read) |

Not proven here: that the scheduled route runs in production. This run used the
command-line form of the same deletion code.
