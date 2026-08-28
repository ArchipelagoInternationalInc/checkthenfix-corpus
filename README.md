# checkthenfix-corpus

The written record of the CheckThenFix build: one report per Builder session, in
`reports/`, kept permanently.

## What is here

`reports/` only. Every session files a report here before it ends — findings,
counts, decisions, and post-mortems. The reports are the project's memory across
sessions and machines, which is why they are public and why they stay.

## What is not here

The test documents used to live at the top level of this repository. They moved
to a private repository on 2026-08-28, following the corpus audit filed as
`reports/2026-08-26-corpus-repository-audit.md`.

Each of those documents is a public record, so none of them was a secret. The
collection was the problem: the documents name their cities, towns and districts
on their front pages, while this project publishes aggregate figures — failure
rates, the canonical document's score — under a standing promise that no entity
is named. Keeping the identifiable sources next to those figures would have let
anyone map a published failing score onto a real town's real document.

Two files left the corpus altogether in the same move, as not being government
documents: a browser print-to-PDF capture of a viewer window, and the
proceedings of a professional medical association.

## Rules for what goes in a report

Counts, findings and decisions. No document contents, no secrets, no
infrastructure hostnames, and no named towns, districts or organizations.
