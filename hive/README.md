# APACHE HIVE

## Requrements
* Hadoop
* Yarn


## Installation
* Choose your most compatible version of hive and extract it into `/my-hadoop-cluster/hive` 
```
> tar -xzvf [hive package] -c /path/to/my-hadoop-cluster/hive
```

## Setup global environment variables for Hive

* Edit `bashrc`: insert `HIVE_HOME`
```
> sudo nano ~/.bashrc
```

```
# HOME
...
export HIVE_HOME=/path/to/my-hadoop-cluster/hive

# PATH
...
export PATH=$PATH:$HIVE_HOME/bin 
```

## Configure Hive

* It is required for HDFS to create a folder to store data for Hive. The first step is to create hive's folder in HDFS

```
> hadoop -fs -mkdir /usr
> hadoop -fs -mkdir /usr/hive
> hadoop -fs -mkdir /usr/hive/warehouse
> hadoop -fs -mkdir /tmp
```
* Set READ/WRITE permission for `/warehouse` and `/tmp`

```
> hadoop fs -chmod g+w /usr/hive/warehouse
> hadoop fs -chmod g+w /tmp
```

* Edit hive environment file `hive-env.sh`

```
cd $HIVE_HOME/conf

#Rename or copy environment template script
cp hive-env.sh.template hive-env.sh

#Make it executable
chmod +x hive-env.sh

#Edit environment script.
sudo nano $HIVE_HOME/conf/hive-env.sh
```

* Inside `hive-env.sh`, add the following variables
```
export HADOOP_HOME=/path/to/my-hadoop-cluster/hadoop
export HADOOP_HEAPSIZE=512
export HIVE_CONF_DIR=/path/to/hive/apache-hive-x.x.x-bin/conf
export JAVA_HOME=path/to/java-classpath
export YARN_HOME=$YARN_HOME
```

* Set `hive-env.sh` file as executable.

```
> chmod +x $HIVE_HOME/conf/hive-env.sh
```

* Enable Hive log

```
> cd $HIVE_HOME/conf
> mv hive-log4j2.properties.template hive-log4j2.properties
> mv hive-exec-log4j2.properties.template hive-exec-log4j2.properties
```

Edit theese 2 files

```
nano hive-log4j2.properties
nano hive-exec-log4j2.properties

#Change "status = INFO" line to "status = ERROR" in the above 2 files
```

* Add or update classpath property in `mapred-site.xml` file. This line may already be correct in `mapred-site.xml`. 

```
> nano  $HADOOP_CONF_DIR/mapred-site.xml
```

inside `mapred-site.xml`
```
<property>
<name>mapreduce.application.classpath</name>
<value>
<!--
refer this to your own path
-->
$HADOOP_CLIENT_CONF_DIR,$HADOOP_CONF_DIR,$HADOOP_COMMON_HOME/*,$HADOOP_COMMON_HOME/lib/*,$HADOOP_HDFS_HOME/*,$HADOOP_HDFS_HOME/lib/*,$HADOOP_YARN_HOME/*,$HADOOP_YARN_HOME/lib/*,$HADOOP_MAPRED_HOME/*,$HADOOP_MAPRED_HOME/lib/*,$MR2_CLASSPATH,/usr/local/hadoop/share/hadoop/mapreduce/*,/usr/local/hadoop/share/hadoop/mapreduce/lib/*,/usr/local/hadoop/share/hadoop/hdfs/*,/usr/local/hadoop/share/hadoop/hdfs/lib/*,/usr/local/hadoop/share/hadoop/common/lib/*,/usr/local/hadoop/share/hadoop/common/*,/usr/local/hadoop/share/hadoop/yarn/lib/*,/usr/local/hadoop/share/hadoop/yarn/*
</value>
</property>
```

* Create new file `hive-site.xml`. This file contains Hive configuration

```
> $HIVE_HOME/conf/hive-site.xml
```

Inside `hive-site.xml`, add meta-store connection URL in this file. Adjust hive path as per your version. Default meta-store is `Derby database` for  Hive. Remove metastore directory if it already exist. This may exist from previous installations.
```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:derby:/path/to/my-hadoop-cluster/hive/apache-hive-x.x.x-bin/conf/metastore_db;create=true</value>
    <description>
      JDBC connect string for a JDBC metastore.
      To use SSL to encrypt/authenticate the connection, provide database-specific SSL flag in the connection URL.
      For example, jdbc:postgresql://myhost/db?ssl=true for postgres database.
    </description>
  </property>
</configuration>  
```

## Hive Initialization
This section informs the actions taken as the preparation for Hive
* Initialize derby metastore for Hive

```
> $HIVE_HOME/bin/schematool -initSchema -dbType derby
```

This would return the exception below
```
Exception in thread "main" java.lang.NoSuchMethodError: com.google.common.base.Preconditions...
```

* To fix this, replace the guava-19.0.jar stored in “$HIVE_HOME\lib” with Hadoop’s guava-27.0-jre.jar found in “$HADOOP_HOME\share\hadoop\hdfs\lib”.

```
> cd $HIVE_HOME/lib

#Remove old guava-*.jar file
> ls guava-*.jar
> rm guava-*.jar

#Copy new jar
> cp /usr/local/hadoop/share/hadoop/common/lib/guava-27.0-jre.jar .
```

Try again
```
> $HIVE_HOME/bin/schematool -initSchema -dbType der
```

## Hive is ready to go

Test hive
```
> hive
hive > show tables;
..
```
