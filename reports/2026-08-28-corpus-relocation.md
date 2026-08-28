# Corpus relocation — the audit's instruction, executed
## 2026-08-28 · Builder session

The PM's corpus audit (`2026-08-26-corpus-repository-audit.md`, filed verbatim
this session) was approved and its proposed Builder instruction executed in
full. One finding needs the owner's decision and is at the bottom.

## The question the audit asked

> "One line from the Builder settles it: tests read the corpus from [local path /
> this repository]."

**Tests read the corpus from a local path — the application repository's own
`corpus/` directory — never from a repository over the network.**

The proof is that the application repository's `corpus/.gitignore` excludes
`*.pdf`, so the documents were never committed there; every code reference is a
relative path like `corpus/docs/...`. The copies in the public record repository
were a shareable backup, not a test input. "Repoint any test or script that reads
corpus files" was therefore a no-op for tests, and the whole test suite passing
untouched after the move is the evidence, not an assurance.

## The three unclear files

Identified by reading each file's metadata and first page.

| File | What it actually is | Verdict |
|---|---|---|
| the "investor meetings" file | 2 pages, produced by a browser's print-to-PDF; its only text is a PDF viewer's page-navigation chrome. A failed download, not a document. | **not a government document — removed** |
| the "bot reports" file | 111 pages, the proceedings of a professional medical association's annual meeting | **not a government document — removed** |
| the numeric-filename file | 499 pages, a county manager's recommended budget book | **is a government document — kept** |

## What was done

1. The audit was committed verbatim to `reports/`.
2. `checkthenfix-corpus-private` created, private, under the Archipelago account.
3. The full public history was pushed into it first, so every document keeps its
   commits; the documents were then moved into `docs/` to match the layout the
   application already expects.
4. The two non-government files were removed.
5. The build machine's local-only corpus material was added — remediated
   outputs, judgment fixtures, the survey mapping, and the measurement tables —
   so the private repository is now the complete corpus, not just the backup.
6. **Verified before deleting anything:** a fresh clone of the private
   repository was checksummed file-by-file against the local corpus. All 30
   remaining documents byte-identical, plus one file the local corpus did not
   have. Only then were the documents removed from the public repository.
7. The canonical check was re-run against the canonical document **as extracted
   from a fresh clone of the private repository**: veraPDF 1.30.2, profile ua1,
   **score 42**. The file's checksum from the private clone matches the local
   copy exactly.
8. The public repository now contains `reports/` and a `README.md` and nothing
   else.

## Counts

- Documents in the public repository before: 33 tracked files (the audit said
  34; the tracked count is 33)
- Removed as not government documents: 2
- Documents in the private repository now: 31
- Reports in the public repository: 39
- Test suite after the move: 46 files, 436 tests, all passing; lint and
  typecheck clean

## A cost the owner should know about

The removed association-proceedings file was the cost model's **text-light**
anchor — the only document in the corpus with zero images across more than a
hundred pages. No remaining government document is close; the lightest runs
about 0.7 images per page, a profile the long-budget anchor already covers.
Rather than relabel a poor substitute and quietly corrupt the model, the
text-light row was removed from the measurement script with the reason recorded
in the file. The historical text-light numbers stand as the record of what was
measured. If the cheap end of the cost curve ever needs re-measuring, the corpus
needs a genuinely text-light **government** document first.

## Open finding — deleting is not removing

The instruction said to remove the documents from the public repository and
confirm it contains only `reports/` and its README. That is done, and it is
satisfied to the letter. **It does not achieve what the audit was for.**

The documents remain in the public repository's history and are still
downloadable by anyone. Verified concretely: a fresh clone of the public
repository is still 63 MB, and any of the 33 documents extracts from the
previous commit with a single command. The audit's Question 2 concern —
connecting a published failing score to a real entity's document — is unchanged
by the delete commit.

Closing it for real means rewriting the public repository's history and
force-pushing, which would invalidate every existing clone and cannot be undone.
That is the owner's call, not the Builder's, so it was **not** done. The
sequence, when approved:

```bash
git clone --mirror git@github.com:ArchipelagoInternationalInc/checkthenfix-corpus.git
cd checkthenfix-corpus.git
git filter-repo --invert-paths --path-glob '*.pdf' --path-glob '*.docx' --force
git push --force --mirror
```

GitHub keeps unreferenced objects for a while after such a push, so the last
step is asking GitHub Support to run garbage collection on the repository.
Until that happens, the documents are still reachable by anyone who knows to
look. The exposure is old, not new — this session did not create it — but it is
now precisely measured rather than assumed closed.
