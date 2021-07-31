# start-on-Angel
steps to get started with Tencent project—Angel<br>
the whole project is built in docker, Ubuntu

## dependencies
1. docker 
2. jav=1.8
3. maven=3.3.9
4. scala=2.13.0
5. protobuf=2.5
6. hadoop=2.7.1
7. hbase=1.4.13
8. spark=2.3.0
9. Angel=2.4.0
10. pytorch_on_Angel=0.2.0
11. pytorch=1.3.1
12. torchvision=0.4.2

## step1：build Psedo-Distributed Hadoop Cluster 
ref：https://zhuanlan.zhihu.com/p/59758201<br>

  #### 1.1 install and run docker in Ubuntu 16.04
  #### 1.1.1 install
  ```
  sudo apt-get install docker
  ```
  #### 1.1.2 run
  ```
  sudo service docker start
  ```
  #### 1.1.3 build network for docker containers
  ```
  sudo docker network create --driver=bridge hadoop
  ```
  you can check the network by following codes
  ```
  sudo docker network ls
  ```
  #### 1.1.4 pull image Ubuntu 16.04 in docker
  ```
  sudo docker pull ubuntu:16.04
  ```
  you can check the iamge by following codes
  ```
  sudo docker images
  ```
  start a container by the pulled image
  ```
  sudo docker run -it ubuntu:16.04 /bin/bash
  ```
  use `exit` to exit the container
  ```
  exit
  ```
  check all the containers in docker
  ```
  sudo docker ps -a
  ```
  start the exited container with `container ID`
  ```
  sudo docker start xxxIDxxx
  ```
  
  #### 1.2 install Java / Scala / Protobuf / Maven
  change the `apt` source
  back up old source list first
  ```
  cp /etc/apt/sources.list /etc/apt/sources_init.list
  ```
  remove the old `sources.list`
  ```
  rm /etc/apt/sources.list
  ```
  add new apt sources
  ```
  echo 
  "deb http://mirrors.aliyun.com/ubuntu/ xenial main
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial main

  deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main

  deb http://mirrors.aliyun.com/ubuntu/ xenial universe
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial universe
  deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates universe

  deb http://mirrors.aliyun.com/ubuntu/ xenial-security main
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main
  deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security universe" > /etc/apt/sources.list
  ```
  update
  ```
  apt update
  ```
  install Vim to edit files
  ```
  apt install vim
  ```
  #### 1.2.1 install Java
  ```
  apt install openjdk-8-jdk
  ```
  check installition
  ```
  java --version
  ```
  #### 1.2.2 install scala
  ```
  wget https://downloads.lightbend.com/scala/2.13.0/scala-2.13.0.tgz
  ```
  extract it to `/usr/local` and name it as `scala-2.13.0`
  ```
  tar -zxvf scala-2.13.0.tgz -C /usr/local/
  ```
  edit `/etc/profile` and add `PATH`
  ```
  vim /etc/profile
  ```
  add `SCALA_HOME` to `PATH`
  ```
  export SCALA_HOME=/usr/local/scala-2.13.0
  export PATH=$PATH:$SCALA_HOME/bin
  ```
  make it effective
  ```
  source /etc/profile
  ```
  check it 
  ```
  scala
  ```
  #### 1.2.3 install protobuf
  download protobuf-2.5.0 in github
  ```
  wget xxxthe_linkxxx
  ```
  extract it to `/usr/local` and name it as `protobuf-2.5.0`
  ```
  tar -zxvf protobuf-2.5.0.tgz -C /usr/local/
  ```
  make it 
  ```
  cd probuf-2.5.0
  ./configure
  make
  make check
  make install
  ```
  check it
  ```
  protoc --version
  ```
  #### 1.2.4 install maven
  download it in http://maven.apache.org/download.cgi <br>
  find maven-3.3.9 `apache-maven-3.3.9-bin.tar.gz` <br>
  ```
  wget xxxthe_linkxxx
  ```
  extract it to `/usr/local`
  ```
  tar -zxvf apache-maven-3.3.9-bin.tar.gz -C /usr/local/
  ```
  and rename is as `maven-3.3.9`
  ```
  mv apache-maven-3.3.9-bin maven-3.3.9
  ```
  
  #### 1.3 build SSH
  #### 1.3.1 install net-tools
  ```
  apt install net-tools
  apt-get install openssh-server
  ```
  get into root dir `~`
  ```
  cd ~
  ```
  generate key, input nothing, just 'ENTER' 
  ```
  ssh-keygen -t rsa -P ""
  ```
  add key to `authorized_keys`
  ```
  cat .ssh/id_rsa.pub >> .ssh/authorized_keys
  ```
  start SSH
  ```
  service ssh start
  ```
  login in without key
  ```
  ssh 127.0.0.1
  ```
  edit `.bashrc` in order to make SSH auto-start
  ```
  vim ~/.bashrc
  ```
  print `i` to get into `INSERT` mode, and insert `service ssh start` at the last line <br>
  print `Esc` and input `:wq` to save and exit
   
  #### 1.4 install Hadoop
  ```
  wget https://archive.apache.org/dist/hadoop/common/hadoop-2.7.1/hadoop-2.7.1.tar.gz
  ```
  extract it to `/usr/local` and name it as `hadoop`
  ```
  tar -zxvf hadoop-2.7.1.tar.gz -C /usr/local/
  mv hadoop-2.7.1 hadoop
  ```
  edit `/etc/profile` and add `PATH`
  ```
  vim /etc/profile
  ```
  add the following text to `PATH`
  ```
  #java
  export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
  export JRE_HOME=${JAVA_HOME}/jre    
  export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib    
  export PATH=${JAVA_HOME}/bin:$PATH
  #hadoop
  export HADOOP_HOME=/usr/local/hadoop
  export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
  export HADOOP_COMMON_HOME=$HADOOP_HOME 
  export HADOOP_HDFS_HOME=$HADOOP_HOME 
  export HADOOP_MAPRED_HOME=$HADOOP_HOME
  export HADOOP_YARN_HOME=$HADOOP_HOME 
  export HADOOP_INSTALL=$HADOOP_HOME 
  export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native 
  export HADOOP_CONF_DIR=$HADOOP_HOM/etc/hadoop
  export HADOOP_LIBEXEC_DIR=$HADOOP_HOME/libexec 
  export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native:$JAVA_LIBRARY_PATH
  export HDFS_DATANODE_USER=root
  export HDFS_DATANODE_SECURE_USER=root
  export HDFS_SECONDARYNAMENODE_USER=root
  export HDFS_NAMENODE_USER=root
  export YARN_RESOURCEMANAGER_USER=root
  export YARN_NODEMANAGER_USER=root
  ```
  make it effective
  ```
  source /etc/profile
  ```
  get into `/usr/local/hadoop/etc/hadoop`
  ```
  cd /usr/local/hadoop/etc/hadoop
  ```
  edit `hadoop-env.sh`
  ```
  export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
  export HDFS_NAMENODE_USER=root
  export HDFS_DATANODE_USER=root
  export HDFS_SECONDARYNAMENODE_USER=root
  export YARN_RESOURCEMANAGER_USER=root
  export YARN_NODEMANAGER_USER=root
  ```
  edit `core-site.xml`
  ```
  <configuration>
    <property>
        <name>fs.default.name</name>
        <value>hdfs://h01:9000</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/home/hadoop2/hadoop/tmp</value>
    </property>
  </configuration>
  ```
  edit `hdfs-site.xml`
  ```
  <configuration>
    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/home/hadoop2/hadoop/hdfs/name</value>
    </property>
    <property>
        <name>dfs.namenode.data.dir</name>
        <value>/home/hadoop2/hadoop/hdfs/data</value>
    </property>
    <property>
        <name>dfs.namenode.http.address</name>
        <value>h01:50070</value>
    </property>
  </configuration>
  ```
  edit ` mapred-site.xml`
  ```
  <configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>
            /usr/local/hadoop/etc/hadoop,
            /usr/local/hadoop/share/hadoop/common/*,
            /usr/local/hadoop/share/hadoop/common/lib/*,
            /usr/local/hadoop/share/hadoop/hdfs/*,
            /usr/local/hadoop/share/hadoop/hdfs/lib/*,
            /usr/local/hadoop/share/hadoop/mapreduce/*,
            /usr/local/hadoop/share/hadoop/mapreduce/lib/*,
            /usr/local/hadoop/share/hadoop/yarn/*,
            /usr/local/hadoop/share/hadoop/yarn/lib/*
        </value>
    </property>
  </configuration>
  ```
  edit `yarn-site.xml`
  ```
  <configuration>
    <property>
      <name>yarn.resourcemanager.hostname</name>
      <value>h01</value>
    </property>
    <property>
      <name>yarn.nodenanager.aux-services</name>
      <value>mapreduce_shuffle</value>
    </property>
    <property>
      <name>yarn.nodenanager.resource.cpu-vcores</name>
      <value>12</value>
    </property>
    <property>
      <name>yarn.scheduler.minimum-allocation-vcores</name>
      <value>1</value>
    </property>
    <property>
      <name>yarn.nodenanager.resource.memory-mb</name>
      <value>30720</value>
    </property>
    <property>
      <name>yarn.scheduler.minimum-allocation-mb</name>
      <value>1</value>
    </property>
    <property>
      <name>yarn.scheduler.maximum-allocation-mb</name>
      <value>30720</value>
    </property>
    <property>
      <name>yarn.log-aggregation-enable</name>
      <value>true</value>
    </property>
    <property>
      <name>yarn.nodenanager.log-aggregation.roll-monitoring-interval-seconds</name>
      <value>3600</value>
    </property>
    <property>
      <name>yarn.nodenanager.remote-app-log-dir</name>
      <value>/tmp/logs</value>
    </property>
  <configuration>
  ```
  
  

## step2：build Angel and pytorch-on-Angel

## step3：run examples
ref：https://earlytobed.notion.site/Angel-2220b7d8f3f446a3aa4c6fc3f65c4c93

## Q&A
1.
