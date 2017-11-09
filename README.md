# Creacion de una imagen:
gcloud compute --project=sysadmingalicia-185319 images create mongo-base --source-disk=mongo-base --source-disk-zone=europe-west2-a

# Create instances experimento 1:
for i in {1..3}
do
gcloud compute instances create mongo-rep-$i --image mongo-base
done

# Configurar replica-set:
replication:
  replSetName: "sysadmingalicia"


# Inicializamos primario:
rs.initiate({_id : "sysadmingalicia", members:[{ _id : 1, host: "localhost:27017" }]})
rs.initiate({_id : "sysadmingalicia", members:[{ _id : 0, host: "mongo-rep-1.c.sysadmingalicia-185319.internal:27017" }]})

# Check configuration:
rs.config()

# Agregamos los otros 2 nodos:
rs.add("10.154.0.3")
rs.add("10.154.0.4")

# Agregamos un nodo retrasado en 1 hora:
 cfg = rs.config()
 cfg.members[2].slaveDelay = 3600
 cfg.members[2].priority = 0

# Agregamos varias entradas:
for (var i = 0; i<50000; i++) {
db.foo.insert({"numero": i}); sleep(1);
}

# Agregamos numero aleatorio

for (var i = 0; i<50000; i++) {
db.foo.insert({"numero": 1000*Math.rand()}); sleep(1);
}

