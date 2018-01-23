# SHARDING (https://docs.mongodb.com/manual/tutorial/deploy-shard-cluster/)

# Creacion de config node
```bash
gcloud compute instances create config --image mongo-base
gcloud compute instances create router --image mongo-base
gcloud compute instances create shard-1 --image mongo-base
gcloud compute instances create shard-2 --image mongo-base
```

## mongodb config node:
```bash
sharding:
  clusterRole: configsvr
```

## mondodb RS shard 1 node:
```bash
sharding:
  clusterRole: shardsvr
replication:
  replSetName: shard1

rs.initiate({_id : "shard1", members:[{ _id : 0, host: "mongo-shard-a-1:27017" }, { _id : 1, host: "mongo-shard-a-2:27017" }, { _id : 2, host: "mongo-shard-a-3:27017" }]})
```

## mondodb RS shard 2 node:
```
sharding:
  clusterRole: shardsvr
replication:
  replSetName: shard2

rs.initiate({_id : "shard2", members:[{ _id : 0, host: "mongo-shard-b-1:27017" }, { _id : 1, host: "mongo-shard-b-2:27017" }, { _id : 2, host: "mongo-shard-b-3:27017" }]})
```

## Configuracion del router, indicando cual/es son los config server:
```
sharding:
  #configDB: <configReplSetName>/cfg1.example.net:27019,cfg2.example.net:27019
  configDB: config
```
