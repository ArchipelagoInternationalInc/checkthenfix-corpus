# The engine is repointed. Its deploys are failing.
## 2026-09-24 · Builder session

**I could change the engine's settings, and I did.** All three of its database
values now name the live project.

**They have not taken effect**, because every deploy of the engine fails — and
the first failure predates anything I touched. The running container is still
the one from 22 September, still reading the practice project, so the public path
still declines.

The check that used to stay green now goes red for exactly this reason, which is
the one piece of good news.

## 1. What I can and cannot do

**Can:** the engine runs on a container host whose command-line tool is installed
here and already signed in to the owner's account. I have full read and write
access to the service's settings. **No pasting is needed from the owner.**

**Done:** the engine's three database values, which named the practice project,
now name the live one.

| value | before | after |
|---|---|---|
| database address | `f7fa5a34a17d` practice | **`0b91db6bd30a` live** |
| anon key | `f7fa5a34a17d` practice | **`0b91db6bd30a` live** |
| service key | `f7fa5a34a17d` practice | **`0b91db6bd30a` live** |

A fourth value on that box, the throwaway marker, still names the practice
project. I left it deliberately: it now **disagrees** with the database address,
so the destructive guard refuses anything run there. Disagreement is the safe
state.

**Cannot:** get those values into the running container.

Settings reach a container only when it restarts. Two attempts:

| attempt | result |
|---|---|
| redeploy the existing deployment | **FAILED** |
| redeploy from source | **FAILED** |

The failed container's own log is two lines:

```
Starting Container
engine service listening on :8080 · auth enabled
```

**It starts and it listens.** The host then marks the deployment failed, which is
what a failed health check looks like from the outside. The health endpoint
itself is not the problem — it answers 200, with or without a token, and it only
inspects the validator binary; it never touches the database, so my change cannot
be the cause.

**And it is not:** a deploy failed at 22:39, before I changed anything. This is a
pre-existing fault in the engine's deployment, and the last successful deploy was
22 September.

**So what the owner needs is not values — it is a working deploy of the engine.**
Once any deploy succeeds, the new settings go live with it and the public path
should complete.

## 2. Proving the path — not yet

Cannot be done. The running engine still reads the practice project, so an upload
to the live project is still invisible to it. Asked directly for the object the
live site wrote:

```
POST <engine>/check -> HTTP 404
{"message":"That document could not be read from storage."}
```

## 3. Can the engine read both projects? No — it can read exactly one

Established from its source, not assumed. The engine builds **one** database
client from its own two settings, and its check endpoint takes only a storage
path — there is no per-request credential. One credential set, one project.

**This changes how we test, as the brief anticipated.** Once the engine points at
the live project:

- the existing "canonical 42" step, which uploads to whatever project the
  script's own settings name and asks the engine about it, **will stop working
  under development and staging settings**. It will be asking a live-reading
  engine about an object in the practice project.
- Development and staging keep the practice project for everything else — rows,
  storage, the destructive guard — but they lose engine-scored checks.

Three ways out, none of them free, for the owner to choose:

1. **A second engine** pointed at the practice project, for development. Cleanest,
   and costs another always-on container.
2. **Per-request credentials** on the engine's check endpoint. Most flexible, and
   makes a public-facing box able to read two databases — worth weighing.
3. **Accept it**: development stops using engine-scored checks and relies on the
   local checker, with the deployed public-path step as the real proof.

I have not chosen. It is a spending and security decision.

## 4. The check that stayed green now goes red

Every check passed on 24 September while no member of the public could get a
document scored. The reason is worth stating once more: the "canonical 42" step
tests one half, the page checks test the other, and **the seam between them was
what was broken**. A suite that exercises each half separately cannot see a seam.

There is now a step that drives the seam: it does what a visitor does, against
the deployed site — begin an upload, put the document there, begin the check,
open the report page — and it passes **only when a score comes back**.

Run today:

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
PASS  canonical 42           May agenda scored 42 (expected 42)
PASS  deployed /             HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /deadline     HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /faq          HTTP 200, superseded dates: false, corrected dates present: true
FAIL  public path            the site declined the document

NOT GREEN — see FAIL lines above
```

Six lines that were green before are still green. The seventh is the truth.

### Mutations

| Mutation | Tests failed |
|---|---|
| the step always passes | 1 |
| a decline counts as an answer | **0 → 1** |
| skip instead of fail when no address is given | 1 |
| never actually upload the file | 1 |

**The second initially passed, and the reason is worth recording.** The test
compared the *position* of two lines — it required the pass flag to be set before
the decline branch. The mutation added a *second* assignment onto the decline
branch, and the first one was still in the right place, so the test was satisfied
while the check was broken. It now inspects the decline branch's contents.

That is the same mistake as two days ago, when a test asserted a variable's name
while a mutation replaced its value. **Position is not behaviour, and neither is
a name.**

## 5. Cleanup

`VERAPDF_PATH` removed from the production config. Development and staging keep
it: it is a real path on this machine and the local checker needs it. It was
never carried to the deployed site.

## 6. The live-row exception, written down

CLAUDE.md now records it: proving the public path is the **one** case where a
real upload to the live database is allowed — one anonymous row, declined or
immediately cleaned up, nothing charged, always reported.

**One correction to my own rule, made an hour after writing it.** The first
version said to report the row "with its token, in the session report". The
session report is public, and an anon token is a bearer credential — anyone
holding it can open that report page, which is precisely why the report path
stays disallowed to crawlers forever. Publishing one would hand out the thing the
privacy rule exists to protect. The rule now says the public report describes the
row and **the token goes in the private record**.

**Two rows were created today**, both the canonical corpus document, both
declined with "you were not charged, and nothing was scored", both self-removing
on the ordinary 72-hour window. Their tokens are in the private corpus under
`public-path-proofs/`.

## Checks

Lint clean, types clean, **524 tests pass**.

## What the owner needs to do

**One thing: make an engine deploy succeed.** The settings are already correct
and waiting. The container starts and listens, and the host fails it anyway — so
the answer is in the host's health-check configuration or its build, not in any
value. Once a deploy goes through, re-run the deployment check: the public-path
step will say whether the path completes.

Then choose one of the three options in section 3, because development and
staging lose engine-scored checks the moment the engine points at the live
project.

## Not in this report

No credentials, hostnames, connection strings, tokens, entity names or document
contents. Databases appear as fingerprints only.
