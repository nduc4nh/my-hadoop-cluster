# Spark

## Requirements
* Hadoop Cluster
* Yarn Resource management
* Optional:
  * Hive
  * Postgres Metastore

## Setup

* Get spark distribution file at (C) and extract into /my-hadoop-cluster/spark

```
tar xzvf <spark-tar-file> -C /path/to/my-hadoop-cluster/spark
```

* Setup spark environment variables (`~/.bashrc`,`/etc/profile`, ..)

```
export SPARK_HOME=/path/to/my-hadoop-cluster/spark
export PYSPARK_PYTHON=/path/to/python
```

* Navigate to `spark-defaults.xml` at `$SPARK_HOME/conf` and set the `spark.master` as `yarn`

```
# spark-defaults.xml

...

spark.master yarn
```

## Spark is ready to go

## Connect spark with Hive

Since spark directly connects to Hive metastore, in this setup section, there's no need in configuring or adjusting` hiveserver2` files. For `spark` module to work with `hive`, the environment or setup of spark must also takes the hive configuration to process. Thus, the configuration of `Hive`, `hive-site.xml` must be added into Spark configuration.

```
sudo ln -s <hive-site.xml file in hive> <spark/conf>
```

For this to work, create a symlink of hive-site and put it in `spark/conf` folder. Since `hive` metastore is based on `postgres` database, the jar file of postgres in `hive/lib` must be copied and put into `spark/jars`.

```
cp <postgres jar file in hive/lib> <spark/jars>
```

### Spark with Hive is ready to go


