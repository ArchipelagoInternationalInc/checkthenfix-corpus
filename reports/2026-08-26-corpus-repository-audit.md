# Corpus repository audit — answers to the portfolio manager's three questions
## 2026-08-26 · PM seat · Investigation only; nothing deleted; owner decision awaited

## What the repository contains today
Thirty-four government documents at the top level (agendas, meeting minutes, and several full budget books — about 82 MB in total) and the `reports/` folder (38 session reports). The documents are the project's original test corpus: the real files the software was proven against, including the canonical test document (2026-MAY-5-AGENDA.pdf, the "42").

## Question 1 — Are the test documents still doing a job?
Partly confirmable from this seat, and the honest answer is in two parts.
The corpus as a *set* is still load-bearing: the canonical document must score its known 42 on every new surface (that rule is standing), the timing and cost models were calibrated against these exact files, and any future engine change should be re-proven against the same set so results stay comparable. So the documents are not dead weight.
What this seat cannot see is *where the automated tests read them from*: the test suite runs on the build machine, and it may read local copies rather than this repository. One line from the Builder settles it: "tests read the corpus from [local path / this repository]." If the answer is "local," the public copies are serving as a shareable backup, not as test inputs. Three files also have unclear provenance and should be identified before any decision: `december-investor-meetings-2015.pdf` (the name does not sound like a municipal document), `a15-bot-reports.pdf`, and `337014943052726pm.pdf`.

## Question 2 — Is there anything that shouldn't be public?
The `reports/` folder is clean: a scan found no keys, passwords, or secret values, no customer documents, and no towns or organizations named. Two reports mention the site's own public web address, which was against the letter of the old rule but is harmless now that the address is the live site.
The test documents are a different story, and this is the audit's main finding. Each document is a public record, so no single file is a secret. But the *collection* has a problem the hygiene rules didn't anticipate: these documents name their cities, towns, and districts on their front pages, and this project has published statements like "the canonical document scores 42/100" and survey statistics about failure rates, always with the promise that no entity is named. Hosting the identifiable source documents in a public repository beside those statements lets anyone connect a published failing score to a real town's real document. We promised aggregate anonymity with one hand; the repository undermines it with the other. No harm has occurred — the audience for this repository has been two AIs and the owner — but the conflict is real and becomes riskier the more attention launch brings.

## Question 3 — Recommendation
**Trim and relocate: move the test documents to a private repository; this public one keeps `reports/` only.**
One move fixes everything found above. The corpus stays intact and version-controlled (satisfying Question 1) but stops being publicly browsable next to our published statistics (fixing Question 2). The public repository becomes purely what it grew into being: the project's permanent written record — lighter to pull, with nothing in it that can embarrass anyone. The reports stay forever per the standing rule; the repository stays alive as their home. Cost of the move: one Builder session to create the private repository, move the files, repoint anything that reads them, and confirm the canonical-document test still passes from its new source.

## Awaiting decision — proposed Builder instruction if the owner approves
Create a private repository `checkthenfix-corpus-private` under the Archipelago account; move all test documents there with history preserved where practical; identify the three unclear files and remove any that are not government documents; repoint any test or script that reads corpus files; run the canonical-document check from the new source and report its 42; remove the documents from the public repository; confirm the public repository contains only `reports/` and its README afterward. File the session report as always.

*Filed by the PM seat. Note on process: this seat reads the repository but holds no write credentials by design; the Builder commits this report on its next session, and it is delivered to the owner directly in the meantime.*
