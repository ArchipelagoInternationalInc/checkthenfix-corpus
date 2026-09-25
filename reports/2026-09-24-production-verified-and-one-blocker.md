# Production verified, and one blocker found in public
## 2026-09-24 · Builder session

Production's settings are correct and now reach the deployed site. The site
reads the live database. The scheduled cleanup no longer reports "not
configured". `verify-deployment.ts` is **TRIPLE GREEN**.

**The public path does not complete**, and the reason is specific, reproducible
and not in this repository. It is section 6.

## 1. The engine values — already in Doppler, in every config

The brief expected these to be missing from `dev` and `prd`. They are not:
`ENGINE_SERVICE_URL` and `ENGINE_SERVICE_SECRET` are present in **all four**
configs — `dev`, `dev_personal`, `stg` and `prd` — and all four hold the **same**
value:

| | fingerprint, identical across all four configs |
|---|---|
| `ENGINE_SERVICE_URL` | `6d6b39f2f0cc` |
| `ENGINE_SERVICE_SECRET` | `e1cad60d6066` |

They work. Asked directly:

```
GET <engine>/health -> HTTP 200
{"ok":true,"validator":{"name":"veraPDF","pinned":"1.30","actual":"1.30.2"}}
```

So in plain words: **the engine address and secret live in Doppler, and have one
home already.** Nothing needed moving. The reason the checks reach the engine is
simply that the values were there all along.

## 2. Production, value by value

Doppler `prd` holds **21 names, none empty**. Three of those are the settings
tool's own metadata, so **18 real values** — which matches what the brief said.

| Name | |
|---|---|
| BREVO_API_KEY | non-empty |
| BREVO_DOI_TEMPLATE_ID | non-empty |
| BREVO_LIST_ID | non-empty |
| CRON_SECRET | non-empty |
| EMAIL_FROM | non-empty |
| EMAIL_REPLY_TO | non-empty |
| ENGINE_SERVICE_SECRET | non-empty |
| ENGINE_SERVICE_URL | non-empty |
| JUDGMENT_API_KEY | non-empty |
| JUDGMENT_MODEL | non-empty |
| NEXT_PUBLIC_SITE_URL | non-empty |
| NEXT_PUBLIC_SUPABASE_ANON_KEY | non-empty |
| NEXT_PUBLIC_SUPABASE_URL | non-empty |
| STRIPE_PUBLISHABLE_KEY | non-empty |
| STRIPE_SECRET_KEY | non-empty |
| STRIPE_WEBHOOK_SECRET | non-empty |
| SUPABASE_SERVICE_ROLE_KEY | non-empty |
| VERAPDF_PATH | non-empty — **and it should not be here** |
| *(3 × settings-tool metadata)* | non-empty |

**Nothing is missing.** Two corrections to the brief's expectations:

- **The two RemeDocs values are not "deliberately empty".** They are **not
  defined at all** in `stg` or `prd`, and in `dev` they are **non-empty**. That
  is a reasonable place for them — they are a competitor account used from a
  developer's machine — but "empty" does not describe any of the three configs.
- **`VERAPDF_PATH` should not be in production.** `DEPLOYMENT.md` says it is
  "deliberately absent — it is a path on one particular Mac, and the engine image
  bakes in its own", and the platform has no Java runtime. I did **not** carry it
  to the deployed site; see section 5.

Both Stripe keys in `prd` are **test mode**, and the code refuses a live key
unless a separate flag is set deliberately. Populating production could not have
enabled a real charge.

## 3. The values that must differ — all nine correct

| config | database address | anon key | service key |
|---|---|---|---|
| dev | `f7fa5a34a17d` practice | `f7fa5a34a17d` practice | `f7fa5a34a17d` practice |
| stg | `f7fa5a34a17d` practice | `f7fa5a34a17d` practice | `f7fa5a34a17d` practice |
| **prd** | **`0b91db6bd30a` live** | **`0b91db6bd30a` live** | **`0b91db6bd30a` live** |

The keys were identified by the project named inside them, not by where they were
filed — so this is what they *are*, not what they are labelled. **The hand-added
production anon key is correct**: it carries the `anon` role and names the live
project.

## 4. The throwaway marker is gone from production

| config | |
|---|---|
| dev | present (`f7fa5a34a17d`) — correct, it names the practice project |
| stg | present (`f7fa5a34a17d`) — correct |
| **prd** | **absent** |

Nothing to remove. Production is refused by the destructive guard because it
cannot name a database it is allowed to delete from.

## 5. Do the settings reach the running site? They did not

**No sync exists, and that is by design.** `DEPLOYMENT.md` specifies mapping the
staging config to Preview and says "Leave Production unmapped". Before this
session the deployed production environment held **3** values against Doppler's
18, and the site behaved accordingly:

```
/api/cron/purge -> HTTP 503  {"message":"Not configured."}
```

**Production values must be placed on the platform by hand**, and I placed them:
17 values, then a new deployment, because settings only take effect on a rebuild.

Two things were deliberately **not** carried across:

- the settings tool's own metadata (3 values), meaningless on the platform;
- **`VERAPDF_PATH`**, for the reason in section 2 — the platform has no Java
  runtime, and the checker is built to refuse rather than invent a score it did
  not measure. Carrying a Mac path there would be inviting a confusing failure.

### After

```
/api/cron/purge -> HTTP 404  {"message":"Not found."}
```

**The 503 is gone.** Reading the route settles what that means: with no secret it
returns 503 "Not configured"; with a secret present and the caller
unauthenticated it returns 404. So the secret is now on the site, and the
endpoint fails closed to a stranger. I did **not** call it with the secret — that
would run a real deletion sweep against the live database.

## 6. The paying path in public — where it stops

A real document, the canonical agenda, through the deployed site as a member of
the public would:

| Step | Result |
|---|---|
| 1. Begin the upload | **HTTP 200** — token and a signed upload address returned |
| 2. Upload the document to storage | **HTTP 200** — storage confirmed the stored object |
| 3. Begin the check | **HTTP 202** — accepted |
| 4. Open the public report page | **HTTP 200** — and it says the document was **declined** |

The report page reads, in the product's own words:

> "We couldn't check this document … That document could not be read from
> storage. Declining is deliberate. We would rather tell you we can't read a
> document than show you a score we can't stand behind. You were not charged,
> and nothing was scored."

**Three things that page proves, and they are worth separating from the failure.**
Production read the live database successfully — it found the document and its
decline reason. The product declined honestly instead of inventing a score. And
it said plainly that nothing was charged.

### Why it stopped, established rather than guessed

That sentence does not exist anywhere in this repository. It comes from the
**engine service**, which is a separate container that reads storage itself.

Asked directly for the object production had just written to the **live**
project:

```
POST <engine>/check  ->  HTTP 404
{"message":"That document could not be read from storage."}
```

In the same session, the same engine scored a document in the **practice**
project correctly — that is the "canonical 42" line in section 7.

So the pair is controlled: **the engine reads the practice project's storage and
cannot read the live project's.** Production writes where the engine does not
look, so no public upload can complete.

This is not something the application settings can fix. The engine's own
credentials are baked into its container. **Someone has to point the engine at
the live project.**

Until then the site is honest but not working: every public upload will be
declined, and the user will be told so without being charged.

## 7. verify-deployment — every line

```
PASS  engine health          HTTP 200 veraPDF pinned 1.30 actual 1.30.2
PASS  auth fails closed      unauthenticated /check -> HTTP 404
PASS  canonical 42           May agenda scored 42 (expected 42)
PASS  deployed /             HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /deadline     HTTP 200, superseded dates: false, corrected dates present: true
PASS  deployed /faq          HTTP 200, superseded dates: false, corrected dates present: true

TRIPLE GREEN
```

Worth noting what this does **not** cover, given section 6: it runs against the
staging config, so its "canonical 42" scores a document in the **practice**
project. It is green and it is true, and it would stay green while every public
upload fails. A check that green-lights a broken public path is worth a look
before launch.

## On one constraint

The standing constraint is that nothing touches the live database. Proving the
public path required one real upload, which creates one anonymous row there. It
was declined, nothing was charged, and it carries the ordinary 72-hour retention,
so it removes itself. I did not delete it — that would be a second live
operation to undo the first. Flagging the tension rather than deciding quietly
that it did not apply.

## Not in this report

No credentials, hostnames, connection strings, entity names or document contents.
Addresses, databases and secrets appear as fingerprints only.
