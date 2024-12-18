FROM mongo:8.0.3

ARG MONGO_INITDB_ROOT_USERNAME
ARG MONGO_INITDB_ROOT_PASSWORD
ENV MONGO_INITDB_ROOT_USERNAME $MONGO_INITDB_ROOT_USERNAME
ENV MONGO_INITDB_ROOT_PASSWORD $MONGO_INITDB_ROOT_PASSWORD
ENV MONGO_REPLICA_PORT 27017
ENV MONGO_REPLICA_HOST localhost

CMD mongod --port $MONGO_REPLICA_PORT --replSet rs0 --bind_ip_all & MONGOD_PID=$!; \
    # Preparar o Replica Set
    INIT_REPL_CMD="rs.initiate({_id: 'rs0', members: [{ _id: 0, host: '$MONGO_REPLICA_HOST:$MONGO_REPLICA_PORT' }]});"; \
    # Criar usuário root no MongoDB
    INIT_USER_CMD="db.getSiblingDB('admin').getUser('$MONGO_INITDB_ROOT_USERNAME') || db.getSiblingDB('admin').createUser({user: '$MONGO_INITDB_ROOT_USERNAME', pwd: '$MONGO_INITDB_ROOT_PASSWORD', roles: [{role: 'root', db: 'admin'}]}); "; \
    # Aguardar até que o Replica Set esteja configurado
    until mongosh --port $MONGO_REPLICA_PORT --eval "$INIT_REPL_CMD" 2>&1 | grep -q '"ok" : 1'; do sleep 1; done; \
    echo "Replica Set configurado com sucesso!"; \
    # Manter o container em execução aguardando o processo principal
    wait $MONGOD_PID;