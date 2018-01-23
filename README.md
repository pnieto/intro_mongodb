[Primera parte: Replicacion](replicaset.md)

# SHARDING (https://docs.mongodb.com/manual/tutorial/deploy-shard-cluster/)

# Creacion de config node
```bash
gcloud compute instances create mongo-config --image mongo-base
gcloud compute instances create mongo-router --image mongo-base
for i in {1..3}
do
gcloud compute instances create mongo-shard-a-$i --image mongo-base
done
for i in {1..3}
do
gcloud compute instances create mongo-shard-b-$i --image mongo-base
done
```

## mongodb config node:
```bash
sharding:
  clusterRole: configsvr
```

## mondodb RS shard a node:
```bash
sharding:
  clusterRole: shardsvr
replication:
  replSetName: sharda

rs.initiate({_id : "sharda", members:[{ _id : 0, host: "mongo-shard-a-1:27017" }, { _id : 1, host: "mongo-shard-a-2:27017" }, { _id : 2, host: "mongo-shard-a-3:27017" }]})
```

## mondodb RS shard b node:
```
sharding:
  clusterRole: shardsvr
replication:
  replSetName: shardb

rs.initiate({_id : "sharda", members:[{ _id : 0, host: "mongo-shard-b-1:27017" }, { _id : 1, host: "mongo-shard-b-2:27017" }, { _id : 2, host: "mongo-shard-b-3:27017" }]})
```
