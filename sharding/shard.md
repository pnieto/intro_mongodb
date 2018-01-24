# SHARDING (https://docs.mongodb.com/manual/tutorial/deploy-shard-cluster/)

## Creacion de config  RS node (Obligatorio desde la 3.4)
```bash
for i in {1..3}
do
gcloud compute instances create config-$i --image mongo-base
done
```

## Creacion de routers
```bash
for i in {1..3}
do
gcloud compute instances create router-$i --image mongo-base
done
```

## Creacion de los nodos Shard:
```bash
for i in {1..5}
do
gcloud compute instances create shard-$i --image mongo-base
done
```

## mongodb RS config:
```bash
sharding:
  clusterRole: configsvr
replication:
  replSetName: rsconfig

for i in {1..3}
do
gcscp mongod.conf-config config-$i:
done

rs.initiate(
  {
    _id: "rsconfig",
    configsvr: true,
    members: [
      { _id : 0, host : "config-1:27019" },
      { _id : 1, host : "config-2:27019" },
      { _id : 2, host : "config-3:27019" }
    ]
  }
)
```

## mondodb shard 1 node:
```bash
sharding:
  clusterRole: shardsvr
```

## mondodb shard 2 node:
```
sharding:
  clusterRole: shardsvr
```

## Configuracion del router, indicando cual/es son los config server:
```
sharding:
  configDB: rsconfig/config-1:27019,config-2:27019,config-3:27019

```
