# pgvector image: Flowise's database, with the vector extension pre-enabled so
# the built-in Postgres vector-store node works without a separate DB.
FROM pgvector/pgvector:pg16

# PGDATA one level below the mount: Railway volumes arrive with lost+found,
# which initdb refuses to initialize into.
ENV POSTGRES_USER=postgres \
    POSTGRES_DB=flowise \
    PGDATA=/var/lib/postgresql/data/pgdata

COPY init-pgvector.sh /docker-entrypoint-initdb.d/init-pgvector.sh
