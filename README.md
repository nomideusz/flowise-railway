# Deploy and Host Flowise with Workers on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/flowise-workers?utm_medium=integration&utm_source=button&utm_campaign=flowise-workers)

This template runs [Flowise](https://flowiseai.com/) in **queue mode**: the main instance serves the UI and API, and a separate worker service executes predictions, document upserts and scheduled runs. Postgres (with pgvector), Redis and a Railway storage bucket come pre-wired. Scale workers up when your agents get busy, without touching the UI.

## About Hosting Flowise with Workers

Four services and one bucket:

- **flowise** — UI + API on port 3000, enqueues jobs to Redis (BullMQ)
- **worker** — consumes the queue; add replicas for more throughput
- **postgres** — pgvector image, so the built-in Postgres vector store works out of the box
- **redis** — the queue, persisted on a small volume
- **storage** bucket — uploads and document-store files, shared by main and worker via S3

Credentials are encrypted with `FLOWISE_SECRETKEY_OVERWRITE`, generated once and referenced by the worker so both sides agree. Auth is Flowise's built-in accounts: the first visit asks you to create the admin user.

## Common Use Cases

- Production chatbots and RAG assistants where a long document upsert must not block the UI
- Teams building agent flows that need more than one execution slot
- Scheduled agent runs that keep working while the main instance redeploys
- A Flowise instance that grows: bump `WORKER_CONCURRENCY` or add worker replicas instead of resizing one big container

## Dependencies for Flowise with Workers Hosting

- Postgres (included, pgvector)
- Redis (included)
- Railway bucket (included)

### Deployment Dependencies

- [Flowise docs: running in queue mode](https://docs.flowiseai.com/configuration/running-in-queue-mode)
- [Flowise environment variables](https://docs.flowiseai.com/configuration/environment-variables)
- [Flowise on GitHub](https://github.com/FlowiseAI/Flowise)

### Implementation Details

**First use:** open the `flowise` service domain and register the admin account. Add your model provider keys under Credentials, build a flow, and predictions run on the worker. Check the worker's deploy logs for `Worker created successfully for queue "flowise-queue-prediction"` to confirm it is attached.

**Storage:** `STORAGE_TYPE=s3` pointed at the Railway bucket. Main and worker are separate containers, so a shared local volume is not possible on Railway; the bucket is what makes uploaded files visible to both.

**Scaling:** each worker handles `WORKER_CONCURRENCY` jobs at once (default 100; flows are mostly waiting on LLM calls). For CPU-heavy flows, add worker replicas in the service settings.

Notes and limits:

- Keep `FLOWISE_SECRETKEY_OVERWRITE` stable. Changing it makes every saved credential unreadable.
- `REDIS_URL` ends in `?family=0` on purpose: Railway's private network is IPv6-only and ioredis defaults to IPv4.
- The first deploy runs database migrations; the healthcheck timeout is raised to 10 minutes to cover it.
- Memory: give `flowise` and `worker` 1 GB or more each. Document upserts with large embeddings need more on the worker.

## Why Deploy Flowise with Workers on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying Flowise with Workers on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
