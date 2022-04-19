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

Edit the `bashrc` file
```
> sudo nano ~/.bashrc
```

Insert environment variable 
```
# HOME 
export JAVA_HOME=/path/to/java/
export HADOOP_HOME=
export HADOOP_HDFS_HOME=
export HADOOP_COMMON_HOME=
export HADOOP_MAPRED_HOME=
export HADOOP_CONFIG_DIR=

# PATH
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
```
