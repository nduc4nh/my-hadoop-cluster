# HADOOP

## SSH Connection

Ensure that any 2 of all servers can ssh to each other. If not, the ssh key need to be generated at each one

```
> ssh-keygen -t rsa -b 4096 -c 
```

Each server stores the ssh key in `id_rsa.pub` from `/.ssh` located in the others. The key is stored in `authorized_keys` from the server in `/.ssh` folder

## Installation
```
These actions are taken in all servers (nodes)!
```

Refer to the link from `A` in `my-hadoop-cluster/README.md`, choose the most compatible hadoop version for your hardwares then install it.
Decompress hadoop setup file
```
tar -xsvf [hadoop setup file] -c /path/to/my-hadoop-cluster/hadoop
```

## Setting up environment 

* Actions taken for: 
  * Namenode
  * Datanode

* Edit the `bashrc` file
```
> sudo nano ~/.bashrc
```

* Insert environment variable 
```
# HOME 
export JAVA_HOME=/path/to/java-classpath
export HADOOP_HOME=/path/to/my-hadoop-cluster/hadoop
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_CONFIG_DIR=/$HADOOP_HOME/etc/hadoop

# PATH
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```

After adding the variables, test the hadoop cli to know whether it works
```
>hadoop
```

* Add variables to `hadoop-env.sh`
```
>sudo nano $HADOOP_CONFIG_DIR/hadoop-env.sh
```

* Insert the following parameters

```
export JAVA_HOME=/path/to/java-classpath

# If the node operates using root permission

export HADOOP_NAMENODE_USER=root
export HADOOP_DATANODE_USER=root

# Specify namenode and datanode log directory

export HADOOP_LOG_DIR=/path/to/my-hadoop-cluster/hadoop/logs
```

## Configure Hadoop
Objectives: config `core-site.xml`, `hdfs-site.xml`, `mapred-site.xml`

### Configure Namenode
* `hdfs-site.xml`
```
<configuration>
<!--
define namenode directory storing metadata
-->
<property>
<name>dfs.namenode.name.dir</name>
<value>file:///nducanh/nducanh/hdfs/namenode</value>
<description>NameNode directory for namespace and transaction logs storage.</description>
</property>


<!--
define datanode directory storing data as blocks
-->
<property>
<name>dfs.datanode.data.dir</name>
<value>file:///nducanh/nducanh/hdfs/datanode</value>
<description>DataNode directory</description>
</property>


<!--
number of replication
-->
<property>
<name>dfs.replication</name>
<value>3</value>
</property>


<!--
checking permission when there's a change in parameters
-->
<property>
<name>dfs.permissions</name>
<value>false</value>
</property>


<!--
-->
<property>
<name>dfs.datanode.use.datanode.hostname</name>
<value>false</value>
</property>

<!--
if the environment does not use DNS reverse procedure, set this to false. If false, only Ip Address is used to identify servers
-->
<property>
<name>dfs.namenode.datanode.registration.ip-hostname-check</name>
<value>false</value>
</property>

</configuration>

```

* `core-site.xml`
```
<configuration>
<property>
<name>fs.defaultFS</name>
<value>hdfs://[ip-address]:[port]/</value>
<description>NameNode URI</description>
</property>
</configuration>
```

* `mapred-site.xml`
```
<configuration>

<property>
<name>mapreduce.application.classpath</name>
<value>
$HADOOP_CLIENT_CONF_DIR,$HADOOP_CONF_DIR,$HADOOP_COMMON_HOME/*,$HADOOP_COMMON_HOME/lib/*,$HADOOP_HDFS_HOME/*,$HADOOP_HDFS_HOME/lib/*,$YARN_HOME/*,$YARN_HOME/lib/*,$HADOOP_MAPRED_HOME/*,$HADOOP_MAPRED_HO$
</value>
</property>

<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
<description>MapReduce framework name</description>
</property>

<property>
<name>mapreduce.jobhistory.address</name>
<value>[ip-address]-[port]</value>
<description>Default port is 10020.</description>
</property>

<property>
<name>mapreduce.jobhistory.webapp.address</name>
<value>[ip-address]:[port]</value>
<description>Default port is 19888.</description>
</property>

<property>
<name>mapreduce.jobhistory.intermediate-done-dir</name>
<value>/mr-history/tmp</value>
<description>Directory where history files are written by MapReduce jobs.</description>
</property>

<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
<description>MapReduce framework name</description>
</property>

<property>
<name>mapreduce.jobhistory.address</name>
<value>[ip-address]:[port]</value>
<description>Default port is 10020.</description>
</property>

<property>
<name>mapreduce.jobhistory.webapp.address</name>
<value>[ip-address]:[port]</value>
<description>Default port is 19888.</description>
</property>

<property>
<name>mapreduce.jobhistory.intermediate-done-dir</name>
<value>/mr-history/tmp</value>
<description>Directory where history files are written by MapReduce jobs.</description>
</property>

<property>
<name>mapreduce.jobhistory.done-dir</name>
<value>/mr-history/done</value>
<description>Directory where history files are managed by the MR JobHistory Server.</description>
</property>

</configuration>

```


### Configure Datanode

* `hdfs-site.xml`

```
<configuration>
<property>
<name>fs.defaultFS</name>
<value>hdfs://[name-node-address]:[name-node-port]/</value>
<description>NameNode URI</description>
</property>
</configuration>
```

* `core-site.xml`

```
<configuration>

<property>
<name>dfs.datanode.data.dir</name>
<value>file:///path/to/my-hadoop-cluster/hdfs/datanode</value>
<description>DataNode directory</description>
</property>

<property>
<name>dfs.replication</name>
<value>3</value>
</property>

<property>
<name>dfs.permissions</name>
<value>false</value>
</property>

<property>
<name>dfs.datanode.use.datanode.hostname</name>
<value>false</value>
</property>

<!--
setup and configure ports for datanode slave here
-->
<property>
<name>dfs.datanode.address</name>
<value>0.0.0.0:9888</value>
</property>


<!--
setup and configure ports for datanode slave here
-->
<property>
<name>dfs.datanode.ipc.address</name>
<value>0.0.0.0:9889</value>
</property>

<!--
setup and configure ports for datanode slave here
-->
<property>
<name>dfs.datanode.http.address</name>
<value>0.0.0.0:9890</value>
</property>

<!--
setup and configure ports for datanode slave here
-->
<property>
<name>dfs.datanode.https.address</name>
<value>0.0.0.0:9891</value>
</property>

</configuration>

```

* `mapred-site`

```
<configuration>
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
<description>MapReduce framework name</description>
</property>
</configuration>

```
