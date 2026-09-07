FROM flowiseai/flowise-worker:3.1.4

# Queue consumer — scale replicas horizontally for throughput. Stateless:
# shares Postgres, Redis, the S3 bucket and FLOWISE_SECRETKEY_OVERWRITE with
# main. /healthz on WORKER_PORT (binds ::, so Railway's prober reaches it).
ENV MODE=queue \
    QUEUE_NAME=flowise-queue \
    DATABASE_TYPE=postgres \
    DATABASE_PORT=5432 \
    DATABASE_NAME=flowise \
    DATABASE_USER=postgres \
    DATABASE_SSL=false \
    STORAGE_TYPE=s3 \
    S3_FORCE_PATH_STYLE=true \
    WORKER_PORT=5566 \
    PORT=5566
