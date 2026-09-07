FROM flowiseai/flowise:3.1.4

# Main instance: UI + API + queue producer. Predictions run on the worker.
# Storage is S3 (a Railway bucket) because main and worker are separate
# containers on Railway and cannot share a local volume; secrets come from
# env so no volume is needed here at all.
ENV MODE=queue \
    QUEUE_NAME=flowise-queue \
    DATABASE_TYPE=postgres \
    DATABASE_PORT=5432 \
    DATABASE_NAME=flowise \
    DATABASE_USER=postgres \
    DATABASE_SSL=false \
    STORAGE_TYPE=s3 \
    S3_FORCE_PATH_STYLE=true \
    NUMBER_OF_PROXIES=1 \
    PORT=3000
