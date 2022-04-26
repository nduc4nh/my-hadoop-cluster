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

The `/user/hive/warehouse` is where Apache Hive stores the pernament data while `/tmp` is the location for temporary data for each active session of the server

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

Edit these 2 files

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
<value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*,$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*,$HADOOP_MAPRED_HOME/share/hadoop/common/*,$HADOOP_MAPRED_HOME/share/hadoop/common/lib/*,$HADOOP_MAPRED_HOME/share/hadoop/yarn/*,$HADOOP_MAPRED_HOME/share/hadoop/yarn/lib/*,$HADOOP_MAPRED_HOME/share/hadoop/hdfs/*,$HADOOP_MAPRED_HOME/share/hadoop/hdfs/lib/*</value>
</property>
<!-- note that for each path $var will be followed by another $var/lib>
```

* Create new file `hive-site.xml`. This file contains Hive configuration

```
> $HIVE_HOME/conf/hive-site.xml
```
The `hive-site.xml` is where we configure the `metastore` and `hiveserver2` settings. There are so many ways to setup configuration for these 2 based on system deployment. In this document, 2 main ways are illustrated:
1. Local metastore service
2. Remote metastore service with `postgreSQL`

### Local metastore service setup

For this setup, the database is `derby` which already supported by Hive in the hive distribution

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
### Remote metastore server setup

* Requirement:
  * Installed postgres
  * Postgres.jar file
   
For short, `postgres` should be installed in your remote system following [this](https://www.postgresguide.com/setup/install/). Next, create an user with password to access postgres database.

```
# after installing postgres, a new user named postgress will be added to the system
> sudo su - postgres 
~# psql
postgres=# [Create new user with password cmd :)]
postgres=# [Create database named metastore]
postgres=#\q
~# exit
```

Next, copy the `postgres--xx .jar` file into `path\to\hive\lib`. The metastore server should be configured to connect to postgres database. Therefore, the hive-site.xml should look like this

```
<configuration>
	<property>
		<name>javax.jdo.option.ConnectionURL</name>
		<value>jdbc:postgresql://[remote-host-ip]:5432/metastore</value>
	</property>

	<property>
		<name>javax.jdo.option.ConnectionDriverName</name>
		<value>org.postgresql.Driver</value>
	</property>

	<property>
		<name>javax.jdo.option.ConnectionUserName</name>
		<value>[your postgres username]</value>
	</property>

	<property>
		<name>javax.jdo.option.ConnectionPassword</name>
		<value>[your postgres password]</value>
	</property>

	<property>
		<name>datanucleus.autoCreateSchema</name>
		<value>false</value>
	</property>

	<property>
		<name>datanucleus.fixedDatastore</name>
		<value>true</value>
	</property>

	<property>
		<name>datanucleus.autoStartMechanism</name> 
		<value>SchemaTable</value>
	</property> 

	<property>
		<name>hive.metastore.event.db.notification.api.auth</name>
		<value>false</value>
		<description>
       Should metastore do authorization against database notification related APIs such as get_next_notification.
       If set to true, then only the superusers in proxy settings have the permission
		</description>
	</property>

#Hive server config
<property>
  <name>hive.server2.enable.doAs</name>
  <value>false</value> 
  </property>

<property>
<name>hive.server2.authentication</name>
<value>CUSTOM</value>
</property>

<property>
<name>hive.server2.custom.authentication.class</name>
<value>hive.test.DagFptAuthenticator</value>
</property>

<property>
  <name>hive.metastore.uris</name>
  <value>thrift://10.14.106.45:9083</value>
  <description>IP address (or fully-qualified domain name) and port of the metastore host</description>
</property>

<property>
    <name>hive.metastore.schema.verification</name>
    <value>false</value>
</property>


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

Run hive metastore
```
hive --service metastore 

# or run in background

nohup hive --service metastore &
```
Run hive server
```
hive --service hiveserver2 -hiveconf hive.metastore.uris="thrift:[you hive metastore server host name or ip]:[metastore server port - default is 9083]"

# or run in background

nohup hive --service hiveserver2 -hiveconf hive.metastore.uris="thrift:[you hive metastore server host name or ip]:[metastore server port - default is 9083]" &


```

