# Kafka

## Installation:

* Go the `apache kafka` website and choose a suitable [distribution](https://kafka.apache.org/downloads)

```
> tar xzvf kafka-x.x.x-src.tgz -C /path/to/my-hadoop-cluster/kafka
```

## Config

* Setup variable environment for kafka
 
 ```
 > nano ~/.bashrc or nano /etc/profile
 ....
 
 #kafka
 KAFKA_HOME=/path/tp/kafka/kafka-x.x.x-src
 KAFKA_CONF_DIR = $KAFKA_HOME/config
..
 
 > source ~/.bashrc or source /etc/profile
```
* Start zookeeper 1st. Configure zookeeper server and client port 
```
> nano $KAFKA_CONF_DIR/zookeeper.properties
....
<change server port>
<change client port>
....

# START ZOOKEEPER
> cd $KAFKA_HOME
> ./bin/zookeeper-server-start.sh -daemon ./config/zookeeper.properties
```

* Start kafka 

```
> cd $KAFKA_HOME
> ./bin/kakfka-server-start.sh -daemon ./config/server.properties
```

