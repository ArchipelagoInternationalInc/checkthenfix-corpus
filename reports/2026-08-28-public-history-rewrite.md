# Public repository history rewritten
## 2026-08-28 · Builder session · owner-approved, irreversible

Follow-up to `2026-08-28-corpus-relocation.md`, whose open finding was that
deleting the documents did not remove them. The owner approved the rewrite. It
is done.

## What changed

| | before | after |
|---|---|---|
| fresh clone size | 63 MB | 264 KB |
| commits on `main` | 43 | 39 |
| documents anywhere in history | 32 | **0** |
| reports | 40 | 40 |
| `README.md` | present | present |

Four commits disappeared because they contained nothing but document uploads;
once the documents were removed those commits were empty. No commit that added
or edited a report was lost, and no report content changed.

## What was preserved deliberately

The `v1` release publishes `verapdf-greenfield-1.30.2-installer.zip` (13
downloads), which the handoff names as how a new machine installs the pinned
validator. Release assets live outside git and survive a rewrite, but the
release needs its tag to stay published — and `v1`'s original commit consisted
only of document uploads, so it ceased to exist.

The tag was therefore re-anchored onto the rewritten history **before** the
push, so `v1` never vanished server-side and the asset's download URL never
broke. Verified after the push: release published, asset present, download count
intact.

Anchoring is now nominal — `v1` marks the rewrite point, not the commit that
once carried it. Nothing automated reads the tag; one handoff line references
the release URL, which still resolves.

## Safety

The original history was preserved in two places before the rewrite: a full
`--all` bundle held on the build machine, and the private corpus repository,
which was created earlier this session by pushing the public repository's
complete history. Nothing was destroyed that does not still exist.

## Verified after the push, on the server

- A fresh clone is 264 KB and contains `README.md` and 40 reports.
- Zero `.pdf` or `.docx` paths in any commit, tree or blob reachable from any
  ref.
- The audit and both session reports read back correctly.
- Release `v1` still published with its asset.

## What is NOT yet closed

**The documents are still retrievable from GitHub by old commit SHA.** Measured,
not assumed:

- The API still serves dropped commits — e.g. the old tagged commit returns
  normally.
- Listing that commit returns **32 documents**.
- The canonical document downloads from it at its full 306,415 bytes.

This is expected. A force-push makes old objects unreferenced; it does not
delete them. GitHub retains unreferenced objects until it garbage-collects the
repository, which only GitHub Support can trigger on demand.

Two facts bound the risk. The repository has **no forks and no network parent**,
so no fork can keep the objects alive independently — GC will be sufficient.
And reaching the objects requires knowing an old SHA, which is not discoverable
from the repository in its current state; it would have to come from a clone
taken before today.

Until Support completes the collection, treat the documents as still public. The
exposure is smaller and precisely bounded, but it is not zero, and this report
should not be read as saying otherwise.
