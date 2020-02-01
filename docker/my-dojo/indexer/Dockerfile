FROM    rust:1.37.0-slim

ENV     INDEXER_HOME        /home/indexer
ENV     INDEXER_VERSION     0.8.2
ENV     INDEXER_URL         https://github.com/romanz/electrs/archive

RUN     apt-get update && \
        apt-get install -y clang cmake git && \
        apt-get install -y libsnappy-dev

# Create group and user indexer
RUN     addgroup --system -gid 1109 indexer && \
        adduser --system --ingroup indexer -uid 1106 indexer

# Create data directory
RUN     mkdir "$INDEXER_HOME/electrs" && \
        mkdir "$INDEXER_HOME/db" && \
        chown -h indexer:indexer "$INDEXER_HOME/electrs" && \
        chown -h indexer:indexer "$INDEXER_HOME/db"

# Copy restart script
COPY    ./restart.sh /restart.sh
RUN     chown indexer:indexer /restart.sh && \
        chmod 777 /restart.sh

# Copy electrs.toml
COPY    ./electrs.toml /electrs.toml
RUN     chown indexer:indexer /electrs.toml && \
        chmod 777 /electrs.toml

# Copy wait-for-it script
COPY    ./wait-for-it.sh /wait-for-it.sh
RUN     chown indexer:indexer /wait-for-it.sh && \
        chmod u+x /wait-for-it.sh && \
        chmod g+x /wait-for-it.sh

# Install electrs
RUN     set -ex && \
        wget -qO electrs.tar.gz "$INDEXER_URL/v$INDEXER_VERSION.tar.gz" && \
        tar -xzvf electrs.tar.gz -C "$INDEXER_HOME/electrs" --strip-components 1 && \
        rm electrs.tar.gz

USER    indexer

RUN     cd "$INDEXER_HOME/electrs" && \
        cargo install --path . 

EXPOSE  50001

STOPSIGNAL SIGINT
