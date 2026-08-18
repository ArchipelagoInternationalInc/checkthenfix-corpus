# Builder session reports

Convention adopted 2026-08-18. After each build slice the Builder commits a
markdown report here so the PM reads it directly instead of the owner
couriering documents between sessions.

## What goes in

Counts, findings, decisions, and what is left open. Enough for someone who was
not in the session to know what changed and what is now believed to be true.

## What never goes in

This repository is **public**.

- **No document contents.** Scores, counts and clause identifiers are results;
  the text inside anyone's PDF is not. Nothing in a report may quote, excerpt,
  or paraphrase the body of a checked document.
- **No secrets.** No API keys, tokens, connection strings, or bearer values —
  not even redacted shapes of them.
- **No infrastructure hostnames.** Deployment URLs and service hosts are access
  points. They are omitted here even when a given URL is technically reachable
  already; a public index of where things live is a different thing from one
  host being resolvable.
- **No entity named or shamed.** Corpus filenames are dates and document types.
  Where a result is reported, it is reported the way the go-to-market rule
  requires: aggregate, or a single document identified only by its neutral
  filename.
