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
for i in {1..2}
do
gcloud compute instances create router-$i --image mongo-base
done
```

## Creacion de los nodos Shard:
```bash
for i in {1..4}
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

for i in {1..4}
do
gcscp mongod.conf-shard shard-$i:
done
```

## Configuracion del router, indicando cual/es son los config server:
```
sharding:
  configDB: rsconfig/config-1:27019,config-2:27019,config-3:27019

for i in {1..2}
do
gcscp mongod.conf-router router-$i:
done

sudo systemctl start mongos.service

```

## Agregar los shard
```
sh.addShard("shard-1:27018")
sh.addShard("shard-2:27018")
```

## Agregamos base de datos y creamos shard key
```
use sysadmingalicia
sh.enableSharding("sysadmingalicia")
sh.shardCollection( "sysadmingalicia.foo", { "number" : "hashed" } )
```

## Agregamos datos a la base de datos
```
use sysadmingalicia
var before = new Date()
for (var i = 0; i<50000; i++) {
db.foo.insert({"number": 10000000000*Math.random(),"data":"janjndandanasnsafsanfnaskjdnjaskndjsandjsnadjksndjasndjksandjksandkjasndnaldaksndlasndlasndlkasndasndlsandjsandlansdlasndl"});
}
var after = new Date()
execution_mills = after - before

```

## Distribucion de los documentos
```
db.foo.getShardDistribution()
```

## Agregar 2 nuevos shard
```
sh.addShard("shard-3:27018")
sh.addShard("shard-4:27018")
```

## Agregamos datos a la base de datos
```
use sysadmingalicia
var before = new Date()
for (var i = 0; i<50000; i++) {
db.foo.insert({"number": 10000000000*Math.random(),"data":"janjndandanasnsafsanfnaskjdnjaskndjsandjsnadjksndjasndjksandjksandkjasndnaldaksndlasndlasndlkasndasndlsandjsandlansdlasndl"});
}
var after = new Date()
execution_mills = after - before

```

## Eliminar 2 shards (volver a ejecutar para ver estado)
```
db.adminCommand( { removeShard: "shard0003" } )
db.adminCommand( { removeShard: "shard0002" } )
```

