## `docker-compose.yml` file

```yml
services:

  mongo1:
    image: mongo:8.0
    container_name: mongo1
    hostname: mongo1
    restart: always
    command: ['mongod', '--replSet', 'rs0', '--bind_ip_all', '--auth', '--keyFile', '/data/mongodb-keyfile']
    entrypoint: ['sh', '-c', 'chmod 600 /data/mongodb-keyfile && exec "$@"', '--']
    ports:
      - 27020:27017
    networks:
      - mongo-net
    volumes:
      - ./data/mongo1-data:/data/db
      - ./mongodb-keyfile:/data/mongodb-keyfile
  
  mongo2:
    image: mongo:8.0
    container_name: mongo2
    hostname: mongo2
    restart: always
    command: ['mongod', '--replSet', 'rs0', '--bind_ip_all', '--auth', '--keyFile', '/data/mongodb-keyfile']
    entrypoint: ['sh', '-c', 'chmod 600 /data/mongodb-keyfile && exec "$@"', '--']
    ports:
      - 27021:27017
    networks:
      - mongo-net
    volumes:
      - ./data/mongo2-data:/data/db
      - ./mongodb-keyfile:/data/mongodb-keyfile
  
  mongo3:
    image: mongo:8.0
    container_name: mongo3
    hostname: mongo3
    restart: always
    command: ['mongod', '--replSet', 'rs0', '--bind_ip_all', '--auth', '--keyFile', '/data/mongodb-keyfile']
    entrypoint: ['sh', '-c', 'chmod 600 /data/mongodb-keyfile && exec "$@"', '--']
    ports:
      - 27022:27017
    networks:
      - mongo-net
    volumes:
      - ./data/mongo3-data:/data/db
      - ./mongodb-keyfile:/data/mongodb-keyfile
  
  haproxy:
    image: haproxy:2.7
    container_name: haproxy
    restart: always
    depends_on:
      - mongo1
      - mongo2
      - mongo3
    ports:
      - 27017:27017
    networks:
      - mongo-net
    volumes:
      - ./haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro
  
networks:
  mongo-net:
```

_Open the port `27017, 27020, 27021, 27022` and create a file called `mongodb-keyfile`_

## `haproxy.cfg` file

```cfg
global
    log stdout format raw local0
  
defaults
    log     global
    mode    tcp
    option  tcplog
    timeout connect 5s
    timeout client  30s
    timeout server  30s
  
frontend mongo_front
    bind *:27017
    default_backend mongo_back
  
backend mongo_back
    server mongo1 <IP>:27020 check inter 3s fall 3 rise 2
    server mongo2 <IP>:27021 check inter 3s fall 3 rise 2 backup
    server mongo3 <IP>:27022 check inter 3s fall 3 rise 2 backup
```

---
## Initiate ReplicaSet

### Connect to any container
```bash
docker exec -it mongo1 mongosh
```

### Run the command to create replicaset
```mongosh
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "<IP>:27020" },
    { _id: 1, host: "<IP>:27021" },
    { _id: 2, host: "<IP>:27022" }
  ]
})
```

_Exit and connect again using the above step, make sure that it is a primary container_

```mongosh
rs.status() # to certify that the replica set is up and running
```

## Create Root User

```mongosh
use admin

db.createUser({
  user: "<USERNAME>",
  pwd: "<PASSWORD>",
  roles: [
    { role: "root", db: "admin" }
  ]
});
```

_If you want you can create the above 3 mongo in separate instance and then assign the instance ip in the replicaset config and haproxy config_



