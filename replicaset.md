# ReplicaSet
## Creacion de una imagen:
```bash
gcloud compute --project=sysadmingalicia-185319 images create mongo-base --source-disk=mongo-base --source-disk-zone=europe-west2-a
```

## Create instances ReplicaSet:
```bash
for i in {1..3}
do
gcloud compute instances create mongo-rep-$i --image mongo-base
done
```

## Configurar replica-set:
```bash
replication:
  replSetName: "sysadmingalicia"
```

## Inicializamos primario:
```bash
rs.initiate({_id : "sysadmingalicia", members:[{ _id : 0, host: "mongo-rep-1:27017" }]})
```

## Check configuration:
```bash
rs.config()
```

## Agregamos los otros 2 nodos:
```bash
rs.add("mongo-rep-2")
rs.add("mongo-rep-3")
```

## Agregamos un nodo retrasado en 1 minuto:
```bash
 cfg = rs.config()
 cfg.members[2].slaveDelay = 60
 cfg.members[2].priority = 0
```

## Agregamos varias entradas:
```bash
for (var i = 0; i<50000; i++) {
db.foo.insert({"numero": i}); sleep(1);
}
```

## Agregamos numero aleatorio
```bash
for (var i = 0; i<50000; i++) {
db.foo.insert({"numero": 1000*Math.rand()}); sleep(1);
}
``` 