---
name: deploying-to-railway
description: Use when deploying an application to Railway, or debugging a Railway deployment that isn't behaving as expected — a service that won't heal, a database that won't connect, a push that didn't deploy, a build that fails only in the real container, a cron service that built but never ran, an environment variable the app can't see, or object storage that won't serve a file. Triggers on "deploy to Railway", "Railway won't deploy", "Railway build failing", "Railway database", "Railway bucket", "Railway storage", "railway run", "railway CLI", "push to production but nothing deployed".
---

# Deploying to Railway

Every rule below was earned on a real deployment, not inferred from documentation. Each cost real time the first time; none should cost it again.

## Before touching infrastructure

**Verify auto-deploy is actually on before assuming a push will deploy anything.** Railway's GitHub integration can be connected — the repo shows as linked, builds have happened before — while the specific service's auto-deploy toggle is off. Nothing about the dashboard makes this obvious at a glance, and no error occurs when a push is silently ignored. `git push` succeeding and a deployment starting are two different events; confirm the second one actually happened (check the service's deployment list for a new entry with the new commit) rather than assuming a successful push implies a deploy in flight. This is the single most confusing failure mode in this list, because everything else *looks* like it worked — the push succeeds, CI (if any) is green, and the only symptom is that the live site quietly keeps serving old code.

**If deploys go through a promoted branch (`production`) separate from the trunk you commit to (`main`), pushing the trunk alone does nothing.** The promoted branch has to be explicitly fast-forwarded and pushed too. This is a deliberate two-step-release pattern, not a mistake — but it means "I pushed" and "it's live" are never the same claim in this setup, and a session that only pushes `main` will spend real time confused about why nothing changed.

**Confirm the sandbox your agent tooling runs in is the same machine the human is on.** If a file your tools report as present the human's own terminal reports as missing (or vice versa), you are in an isolated sandbox — this silently breaks anything requiring real browser interaction, such as `railway login`'s device-code or browser-based OAuth flow. In that situation, CLI login cannot be completed from agent tool calls at all; hand the login step to the human's own terminal and do infrastructure changes through an authenticated MCP/API connector instead of the CLI.

**A build that "succeeded" is not a build that got promoted.** Railway will not promote a deployment whose Pre-Deploy Command fails — and it is quiet about it. The previous good image keeps serving, so there is no outage, no alert, and nothing in the app's behaviour to notice; two consecutive deploys can fail this way and the site looks perfectly healthy the whole time. If a change hasn't appeared live, check the deployment's Pre-Deploy step specifically rather than trusting a green build or a healthy `/health`.

## The CLI

**`railway run` injects nothing unless you pass `--service`, even after `railway link` named that service.** `railway link --project X --environment production --service api` reports success, and `railway status` confirms the project and environment — but `railway run <cmd>` still hands the child process *no* service variables unless `--service api` is repeated on the run itself. The failure mode is what makes this expensive: the command succeeds and the program simply sees no variables, which is indistinguishable from the variables never having been set. The wrong diagnosis ("you must not have added them") is the natural one. **When a variable "isn't there", check whether it is being *injected* before concluding it was not *set*** — `railway variables --service <name>` shows the truth, and piping it through something that prints only the keys keeps the values out of your terminal scrollback.

**`railway link` takes flags, so it does not need an interactive terminal.** `railway link --project <id> --environment <env> --service <name>` skips the arrow-key picker entirely, which matters when driving it from a non-interactive shell or an agent tool that cannot answer a prompt.

## Object storage / buckets

**Buckets are private and there is no public-read option.** "Public buckets are currently not supported" — the only ways to serve an object are a **presigned URL** or **proxying it through a backend service**. This is fine for a one-shot fetch by a third party (an API that takes an `image_url` and retrieves it once), and it is a real design constraint for anything user-facing: presigned URLs expire, so a public gallery must proxy through your own app rather than mint URLs per page render. Decide which of those two you need *before* choosing a bucket over simply committing the file into a static directory your app already serves.

**The bucket's "Add to service" dialog writes different variable names than its Credentials tab shows.** Under the default "AWS SDK (Generic)" style it writes `AWS_S3_BUCKET_NAME`, `AWS_ENDPOINT_URL`, `AWS_DEFAULT_REGION`, `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` — while the Credentials tab lists the bare `BUCKET`, `ENDPOINT`, `REGION`, `ACCESS_KEY_ID`, `SECRET_ACCESS_KEY`. **Read whichever names the platform actually writes rather than renaming to suit your code**; a rename is a step to get wrong on a machine nobody has configured for six months. Accepting both shapes costs a few lines and removes the whole class of problem.

**A bucket's region cannot be changed after creation**, and the real bucket name is your display name plus a short hash — so neither is safe to hardcode. Pick the region deliberately at creation time, to match where the services that read it run.

**"Add to service" wires references, not secrets.** It writes `${{bucket-name.ACCESS_KEY_ID}}` style expressions, which the platform resolves at deploy time — so credentials never enter a clipboard, a file, or a chat transcript. Combined with `railway run --service`, this means a local script can use production credentials without any of them ever being written to disk. Prefer this to copying values by hand.

## Database provisioning

**A raw-image database service (e.g. a plain `postgres`/`pgvector` Docker image, not Railway's own templated database) does not get the conveniences a templated one does.** No auto-populated `DATABASE_URL` — compose it by hand from the individual `POSTGRES_USER`/`POSTGRES_PASSWORD`/`POSTGRES_DB` variables plus the service's private domain. No `railway connect --tunnel-only` support — that command only recognizes officially templated databases, and fails with an unhelpful "no supported database found" on a raw image. Know this going in, rather than discovering it after trying the templated-database workflow against a raw image.

**A TCP proxy on a raw-image database is not reliable.** Expect it to sometimes never produce a working public address (a conflicting auto-created HTTP domain on the same port is a known trigger), even after apparent reconfiguration. Don't spend more than one attempt on it — private-networking-only, with all access happening from other services inside the same Railway project, is the resilient fallback and often the better long-term choice anyway. If you genuinely need to get a one-time payload of local data into the database, a temporary content-snapshot baked into the image for a single deploy (then reverted) is a more reliable path than fighting the proxy.

**Mind account-level volume size caps.** They exist, they are not always documented where you'd look, and a volume request above the cap can fail silently rather than with a clear "too large" error. Provision close to actual current need — a few hundred MB of headroom, not a round number picked for comfort — both to stay under an undiscovered cap and because it's easy to grow a volume later and hard to shrink one.

**Point `PGDATA` at a subdirectory of the mount, not the mount root.** Railway volumes commonly seed the mount point with a `lost+found` directory; Postgres refuses to initialize a data directory that already contains unexpected entries and crash-loops. Setting `PGDATA=/var/lib/postgresql/data/pgdata` (or equivalent) sidesteps this entirely.

## Dockerfiles and builds

**A local build passing is not evidence a Railway build will pass.** Differences that only surface in the real container: a build context that doesn't include a directory the container actually needs at runtime (a `migrations/` folder for a pre-deploy migrate step, a shared `tsconfig.base.json` a monorepo package extends), a shallow container filesystem that breaks code assuming a deep local-checkout path structure (e.g. walking `__file__`'s parents a fixed number of levels to find a sibling directory — guard this, don't assume it), or a repo-root vs. per-service build context mismatch for a service that imports from a shared package. Treat the first real Railway build of a new service as a genuine test, not a formality — it is where these surface, not in local dev.

**Frontend build-time env vars need to be threaded through as Docker build `ARG`s, not just runtime environment variables.** A framework that statically bakes public env vars at build time (Next.js's `NEXT_PUBLIC_*` is the common case) will silently ship whatever value was present when `docker build` ran — commonly a `localhost` default — unless the Dockerfile declares matching `ARG`/`ENV` pairs in the build stage and the platform is told to pass them as build arguments, not just runtime ones.

**A non-root container user needs explicit ownership of anything it writes to.** Combining a non-root `USER` directive with a framework that writes a cache directory at runtime (Next.js's `.next/cache` under standalone output is the common case) needs `--chown` on the relevant `COPY` lines and/or a pre-created, pre-chowned directory — otherwise the app boots fine and then silently fails to write its cache (`EACCES`), degrading a feature (ISR revalidation) without crashing or logging anywhere obvious.

**A `preDeployCommand`'s multiple steps are not shell-interpreted by default.** `"migrate && seed"` as a literal string is not executed the way it would be in a shell — wrap it explicitly as `sh -c "migrate && seed"` if you need shell semantics (chaining, `&&`, redirects).

**Region-pin config is not additive by default.** If a platform config field expresses "which regions this service runs in" as a map, explicitly nulling out the previous/default region is often required — otherwise a region change silently becomes a region *addition*, doubling replicas across regions rather than moving them.

**A platform's own "redeploy" action may reuse a cached build rather than building fresh.** If you need to prove a specific new commit's code is actually running — not just that *a* deploy succeeded — trigger a genuinely fresh build from that commit explicitly, rather than trusting a generic "redeploy latest" action to have picked up new source.

## Scheduled / cron services

**Triggering a "deploy" on a cron service only builds the image — it does not run the job.** The deployment goes green and reports SUCCESS, which reads exactly like "the script ran" and means only "the image built". Execution happens at the next scheduled tick, or via a manual Run/Trigger in the dashboard. If you need a cron job to execute *now* to verify it works, wait for a real tick or trigger it by hand; do not accept a successful deploy as evidence the job did anything.

**A cron schedule and always-on mode are mutually exclusive on one service.** A scheduled job needs its own dedicated service even when it reuses the exact same Dockerfile and build as an always-on sibling.

## Runtime behavior behind a platform's edge proxy

**A framework needs to be told it's behind a reverse proxy, or client-IP-dependent logic silently breaks.** Rate limiting, IP-based dedupe, or anything else keyed on the requester's IP address will see the proxy's IP for every request unless the framework is explicitly configured to trust forwarded headers (e.g. Fastify's `trustProxy: true`). Nothing crashes; every distinct visitor just quietly collapses into "the proxy," which shows up as broken rate limiting or a dedupe window that never dedupes.

**A raw/manual HTTP response bypasses the framework's own middleware.** Anything that writes directly to the underlying response object (streaming SSE by hand via a raw `writeHead()`/`write()` path is the common case) skips whatever plugin pipeline normally adds headers — CORS being the one that bites hardest, since the failure mode is a browser-side network error with no server-side clue. If a raw-response code path needs CORS (or any other header a plugin normally supplies), set it by hand on that path specifically.

## Agent-tooling specific

**No agent or tool can read a service's variable *values*.** This is a deliberate secret boundary, not a missing feature, and it holds across MCP tools, the conversational infra-agent, and anything else running outside the service. Plan around it rather than hunting for the tool that would let you through: use `${{Service.VAR}}` reference expressions to wire a secret from one service to another **without ever seeing it**, and hand any genuinely unavoidable "read the value" step to the human's own dashboard session. A bucket is not a service, so bucket credentials are not reachable even by the variable-listing tools that work on services.

**A conversational infra-agent tool is usually billable; prefer the direct tools.** Listing deployments, reading logs, fetching service config, setting variables and reading metrics all have direct equivalents that cost less and fail more legibly. Reserve the agent for what the direct tools genuinely cannot do.

**An infra-agent transport error does not mean the action failed.** A call can fail at the transport layer (a truncated-chunk or unexpected-EOF error) while the underlying action succeeded — every time, in one observed session. Verify with a direct read tool before retrying, or you will do the thing twice.

**A conversational infra-agent tool can intermittently return an empty result — a blank or preamble-only response with no actual actions taken — without erroring.** This looks identical to "the request wasn't understood" but is really "the call didn't complete." Retry a small, bounded number of times (two or three); if it keeps happening, it's a transient issue with that specific tool call path, not something a fourth retry fixes — stop and either use a more direct tool/API call for the same action, or hand the step to the human to do in the dashboard directly.

**Never run two dev-server (or any build-output-writing) processes against the same build cache directory concurrently.** Two `next dev` (or equivalent) instances writing to the same `.next` directory at once will corrupt it in a way that only shows up as a cryptic runtime `ENOENT` on an unrelated file, not as an obvious "already running" error. Check what's already listening on relevant ports before starting a new instance, and if you do start one, don't start a second later in the same session without stopping the first.
