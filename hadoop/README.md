# HADOOP

## SSH Connection

Ensure that any 2 of all server can ssh to each other. If not, the ssh key need to be generated at each servers

```
> ssh-keygen -t rsa -b 4096 -c 
```

Each server stores the ssh key in `id_rsa.pub` from `/.ssh` located in the others. The key is stored in `authorized_keys` from the server `/.ssh` folder

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



### Configure Datanode
