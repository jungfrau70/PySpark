Prerequsites:
- deploy server with docker engine and docker-compose


#########################################################################################
# 1. (deploy-server) Preparation to start services
#########################################################################################

export WORKDIR='/root/PySpark/Step3_setup_spark_cluster/5_Spark/'
cd $WORKDIR

## Generate ssh keys
ssh-keygen -t rsa

cd ~/.ssh
cat id_rsa.pub > authorized_keys

## Instanticate the containers
rm -rf db.sql/ hive-postgres-data/ spark-apps/ spark-data

docker-compose up -d

docker stats

## Clean up except the running container
# docker system prune -a

#########################################################################################
# 2. (deploy-server) Distribute ssh-keys
#########################################################################################

## Set root's password for ssh key exchange
nodes='master worker1 worker2'
for node in $nodes
do
    echo $node
    docker exec -it $node passwd
done

## Remove existing known_hosts for ReSet
rm -rf ~/.ssh/known_hosts*

## Add nodes to new known_hosts in deploy-server(=master)
nodes='master worker1 worker2'
for node in $nodes
do 
  ssh root@$node
done

## Copy authorized_keys to workers
nodes='worker1 worker2'
for node in $nodes
do
    docker exec -it $node rm -rf ~/.ssh
    ssh-copy-id root@$node
done

## (if required) Create docker iamges
docker commit master jungfrau70/ubuntu18.04:de-master.2
docker commit worker1 jungfrau70/ubuntu18.04:de-worker.2

docker login

docker push jungfrau70/ubuntu18.04:de-master.2
docker push jungfrau70/ubuntu18.04:de-worker.2

## Check if ssh works
docker exec -it master ssh worker1
docker exec -it master ssh worker2

#########################################################################################
# 3. (deploy-server) Hadoop namenode
#########################################################################################

## (If required) Format namenode
./stop-hadoop-cluster.sh && ./stop-spark-cluster.sh

nodes='master worker1 worker2'
for node in $nodes
do
    docker exec $node rm -rf /home/hadoop/tmp/
done
docker exec master /opt/hadoop/bin/hdfs namenode -format


#########################################################################################
# 4. Setup Hive metastore with PostgrSQL 9.2.24
     Now,Lets Set up Postgress Image in the Docker Container
#########################################################################################
docker exec \
    -it hive-postgres \
    psql -U postgres
	
### Createa database "metastore" for hive in postgress.
CREATE DATABASE metastore;
CREATE USER hive WITH ENCRYPTED PASSWORD 'go2team';
GRANT ALL ON DATABASE metastore TO hive;	

## Validate if Metastore database exists
\l to list
\q to exit postgress

#########################################################################################
# 5. Initialize Hive
#########################################################################################
## Initialize Hive Metastore
docker exec -it master /opt/hive/bin/schematool -dbType postgres -initSchema

docker exec \
    -it hive-postgres \
    psql -U postgres \
    -d metastore
\d
\q
\q

#########################################################################################
# 6. (deploy-server) Start Cluster
#########################################################################################

## Start cluster
~/start-hadoop-cluster.sh && \
~/start-spark-cluster.sh

## Stop cluster
~/stop-spark-cluster.sh && \
~/stop-hadoop-cluster.sh

## Check cluster
~/check-cluster.sh

## Running process in hadoop/spark cluster
master
1586 Jps
1462 Master
537 NameNode
1083 ResourceManager
780 SecondaryNameNode

worker1~4
339 NodeManager
501 Worker
217 DataNode
604 Jps


#########################################################################################
# 6. (deploy-server) Create directories in hdfs 
#########################################################################################

cat >configure-directories.sh<<EOF
source /opt/hadoop/etc/hadoop/hadoop-env.sh
### create directories for logs and jars in HDFS. 
hdfs dfs -rm -r /spark-jars
hdfs dfs -rm -r /spark-logs

hdfs dfs -mkdir /spark-jars
hdfs dfs -mkdir /spark-logs
hdfs dfs -mkdir -p /apps/hive/warehouse

hdfs dfs -ls /spark-jars
hdfs dfs -ls /spark-logs
hdfs dfs -ls /apps/hive/warehouse

### Copy Spark jars to HDFS folder as part of spark.yarn.jars.
hdfs dfs -put /usr/local/spark/jars/* /spark-jars
hdfs dfs -put /opt/hive/lib/postgresql-42.2.24.jar /spark-jars

hdfs dfs -ls /spark-jars/postgresql-42.2.24.jar
EOF

chmod u+x configure-directories.sh

docker cp configure-directories.sh master:/root/
docker exec -it master /bin/bash /root/configure-directories.sh


#########################################################################################
# 7. (deploy-server) Start Hive Server2 & Spark History Server
#########################################################################################

~/start-hive-server2.sh && \
~/start-spark-history-server.sh


#########################################################################################
# 8. (master) Setup Jupyter server
#########################################################################################
#pip install jupyter_contrib_nbextensions

docker exec -it master /bin/bash
env

jupyter notebook --generate-config
jupyter notebook password

cat >/root/.jupyter/jupyter_notebook_config.py<<EOF
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.port = 8888
c.NotebookApp.open_browser = False
c.NotebookApp.allow_root = True
EOF

export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS=notebook

### Move to working direcotry
cd ~/workspace

/usr/local/spark/bin/pyspark

#########################################################################################
# 9. (deploy-server) Stop Cluster
#########################################################################################

~/stop-spark-cluster.sh && \
~/stop-hive-server2.sh && \
~/stop-hadoop-cluster.sh && \
~/stop-spark-history-server.sh

#########################################################################################
# 8. (master) Test Spark cluster
#########################################################################################

docker exec -it master /bin/bash

## Move to working direcotry
cd ~/workspace

## Read env
source /usr/local/spark/conf/spark-env.sh

## Start pyspark with jupyter
export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS='notebook'

cd ~/workspace
pyspark --packages org.mongodb.spark:mongo-spark-connector_2.12:3.0.1

## Examples
pyspark --packages org.mongodb.spark:mongo-spark-connector_2.12:3.0.1

pyspark --conf "spark.mongodb.input.uri=mongodb://root:go2team@mongo/Quake.quakes?authSource=admin" \
        --conf "spark.mongodb.output.uri=mongodb://root:go2team@mongo/Quake.quakes?authSource=admin" \
        --packages org.mongodb.spark:mongo-spark-connector_2.12:3.0.1

pyspark --packages org.postgresql:postgresql:42.3.3

--------------------------------------------------------------------------------------------

### Only if hadoop cluster is running
pyspark --master yarn (default)
pyspark --master local

### Only if spark cluster is running
pyspark --master spark://master:7077
pyspark --master local

### Validate Spark using Python 
/opt/spark/bin/pyspark --master (?)

spark.sql('show databases').show()
spark.sql('create database test').show()
spark.sql('use test').show()
spark.sql('create table spark (col1 int)').show()
spark.sql('show tables').show()
spark.sql('insert into spark values(20)')
spark.sql('select * from spark').show() 
exit()

### Validate the custom table location
docker exec -it master /bin/bash
hdfs dfs -ls /apps/hive/warehouse
hdfs dfs -mkdir -p /apps/hive/warehouse

### Start pyspark with jupyter
set PYSPARK_DRIVER_PYTHON=jupyter
set PYSPARK_DRIVER_PYTHON_OPTS='notebook'


source /usr/local/spark/conf/spark-env.sh
export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS='notebook'


#########################################################################################
# 10. Build the docker image for cluster 
#########################################################################################

docker ps -a

## build custom docker image
docker exec master lsb_release -a
docker commit master jungfrau70/ubuntu18.04:de-master.3
docker commit worker1 jungfrau70/ubuntu18.04:de-worker.3

## push customer docker image
docker image ls
docker login
docker push jungfrau70/ubuntu18.04:de-master.3
docker push jungfrau70/ubuntu18.04:de-worker.3

## show layers of docker image
docker history jungfrau70/ubuntu18.04:de-master.3
docker history jungfrau70/ubuntu18.04:de-worker.3

#########################################################################################
# 11. Backup and restore in VMware Workstation Player
#########################################################################################

Copy folder and rename it
