# start-on-Angel
steps to get started with Tencent project—Angel<br>
the whole project is built in docker, Ubuntu

## dependencies
1. docker 
2. java=1.8
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

## step1：build Pseudo-Distributed Hadoop Cluster 
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
  java -version
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
  wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
  ```
  extract it to `/usr/local` and name it as `protobuf-2.5.0`
  ```
  tar -zxvf protobuf-2.5.0.tar.gz -C /usr/local/
  ```
  make it 
  ```
  cd protobuf-2.5.0
  ./configure
  make
  make check
  make install
  ```
  check it
  ```
  protoc --version
  ```
  if error
  ```
  protoc: error while loading shared libraries: libprotoc.so.8: cannot open shared object file: No such file or directory
  ```
  you need add `usr/local/lib` to `/etc/ld.so.conf`
  ```
  vim /etc/ld.so.conf
  add --> usr/local/lib
  ldconfig
  ```
  #### 1.2.4 install maven
  download it in http://maven.apache.org/download.cgi <br>
  find maven-3.3.9 `apache-maven-3.3.9-bin.tar.gz` <br>
  ```
  wget https://archive.apache.org/dist/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
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
  edit `slaves`
  ```
  vim slaves
  ```
  add master `h01` and slave `h02` <br>
  if you need, you can add more slaves, for example `h03`  `h04` ...
  ```
  h01
  h02
  ```
  
  #### 1.5 build h01 and h02 containers
  export the Ubuntu container as an iamge
  ```
  sudo docker commit -m "haddop" -a "hadoop" xxContainer_IDxxx newhadoop
  ```
  run container `h01`
  ```
  sudo docker run -it --network hadoop -h "h01" --name "h01" -p 50070:50070 -p 8088:8088 newhadoop /bin/bash
  ```
  run container `h02`
  ```
  sudo docker run -it --network hadoop -h "h02" --name "h02" newhadoop /bin/bash
  ```
  format Hadoop
  ```
  cd /usr/local/hadoop/bin#
  ./hadoop namenode -format
  ```
  start Hadoop
  ```
  cd /usr/local/hadoop/sbin/
  ./start-all.sh 
  ```
  you can check info on web h01:8088 / h01:50070 <br>
  check the hadoop state by `./hadoop dfsadmin -report`
  
  #### 1.6 install hbase
  ```
  wget https://mirrors.nav.ro/apache/hbase/1.4.13/hbase-1.4.13-bin.tar.gz
  tar -zxvf hbase-1.4.13-bin.tar.gz -C /usr/local/
  ```
  edit `/etc/profile` by `vim /etc/profile`, add
  ```
  export HBASE_HOME=/usr/local/hbase-1.4.13
  export PATH=$PATH:$HBASE_HOME/bin
  ```
  then `source /etc/profile` <br>
  next, you need do it again in `h02` as well as `h03` ...<br> 
  ```
  ssh h02
  xxx the same as above step xxx
  ```
  cd `/usr/local/hbase-1.4.13/conf`
  edit `hbase-env.sh`
  ```
  export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
  export HBASE_MANAGES_ZK=true
  ```
  edit `hbase-site.xml`
  ```
    <configuration>
      <property>
        <name>hbase.rootdir</name>
        <value>hdfs://h01:9000/hbase</value>
      </property>
      <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
      </property>
      <property>
        <name>hbase.master</name>
        <value>h01:60000</value>
      </property>
      <property>
        <name>hbase.zookeeper.quorum</name>
        <value>h01,h02</value>
      </property>
      <property>
        <name>hbase.zookeeper.property.dataDir</name>
        <value>/home/hadoop/zoodata</value>
      </property>
  </configuration>
  ```
  edit `regionservers`
  ```
  h01
  h02
  ```
  copy the files to h02 ...
  ```
  scp -r /usr/local/hbase-1.4.13 root@h02:/usr/local/
  ```
  
  #### 1.7 install Spark
  ```
  wget https://archive.apache.org/dist/spark/spark-2.3.0/spark-2.3.0-bin-hadoop2.7.tgz
  tar -zxvf spark-2.3.0-bin-hadoop2.7.tgz  -C /usr/local/
  cd /usr/local/
  mv spark-2.3.0-bin-hadoop2.7 spark-2.3.0
  ```
  edit `/etc/profile`
  ```
  export SPARK_HOME=/usr/local/spark-2.3.0
  export PATH=$PATH:$SPARK_HOME/bin
  ```
  make it effective
  ```
  source /etc/profile
  ```
  you need do it again in `h02` as well as `h03` ...<br> 
  ```
  ssh h02
  xxxxxxx
  ```
  edit conf `cd /usr/local/spark-2.4.0/conf`
  ```
  mv spark-env.sh.template spark-env.sh 
  ```
  edit `spark-env.sh`
  ```
  export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
  export HADOOP_HOME=/usr/local/hadoop
  export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop
  export SCALA_HOME=/usr/share/scala

  export SPARK_MASTER_HOST=h01
  export SPARK_MASTER_IP=h01
  export SPARK_WORKER_MEMORY=4g
  ```
  rename `slaves.template` as `slaves`
  ```
  mv slaves.template slaves  
  ```
  edit `slaves`
  ```
  h01
  h02
  ```
  copy files to h02
  ```
  scp -r /usr/local/spark-2.3.0 root@h02:/usr/local/
  ```

## step2：build Angel and pytorch-on-Angel

  #### 2.1 spark-on-Angel
  ref:https://github.com/Angel-ML/angel/blob/branch-3.2.0/docs/tutorials/spark_on_angel_quick_start.md <br>
  
  #### 2.1.1 get Angel-2.4.0<br>
  ref:https://github.com/Angel-ML/angel/blob/master/docs/deploy/source_compile_en.md <br>
  get angel-branch-2.4.0 from github <br>
  run the following command in the root directory of the source code:
  ```
  mvn clean package -Dmaven.test.skip=true
  ```
  angel-2.4.0-bin.zip will be generated under dist/target <br>
  
  extract angel-2.4.0-bin.zip<br>
  #### 2.1.2 edit conf
  edit `angel-2.4.0-bin/bin/spark-on-angl-env.sh` <br>
  edit `SPARK_HOME`, `ANGEL_HOME`, `ANGEL_HDFS_HOME`
  ```
  SPARK_HOME=/usr/local/spark-2.3.0
  ANGEL_HOME=/usr/local/angel-2.4.0-bin
  ANGEL_HDFS_HOME=hdfs://h01:9000
  ANGEL_VERSION=2.4.0
  ```
  #### 2.1.3 upload files
  upload files in `angel-2.4.0-bin` to hdfs
  ```
  hdfs dfs -put /usr/local/angel-2.4.0-bin/bin/ /
  hdfs dfs -put /usr/local/angel-2.4.0-bin/conf/ /
  hdfs dfs -put /usr/local/angel-2.4.0-bin/data/ /
  hdfs dfs -put /usr/local/angel-2.4.0-bin/lib/ /
  ```
  ```
  source ./spark-on-angel-env.sh
  ```
  check conf by running `angel-2.4.0-bin/bin/SONA-example`
  ```
  cd angel-2.4.0-bin/bin; 
  ./SONA-example
  ```
  #### 2.2 pytorch-on-angel
  ref:https://github.com/Angel-ML/PyTorch-On-Angel<br>
  #### 2.2.1 get pytorch-on-angel-0.2.0
  get pytorch-on-angel-0.2.0 from github <br>
  change contradic settings <br>
  edit `Dockerfile`
  ```
  torchvision=0.4.2
  ...
  ENV TORCH_HOME=/opt/libtorch/
  ENV Torch_DIR=/opt/libtorch/share/cmake/Torch
  ```
  or you can choose to edit `cpp/CMakeList.txt`
  ```
  set(TORCH_HOME $ENV{TORCH_HOME})
  ```
  the generated files 'pytorch-on-angel-0.2.0.jar' 'pytorch-on-angel-0.2.0-jar-with-dependencies.jar' and 'torch.zip' <br>
  are in ./dist by <br>
  ```
  ./build.sh
  ```
  #### 2.2.2 get script
  below script will generate a deepfm model 'deepfm.pt' in ./dist <br>
  ```
  ./gen_pt_model.sh python/recommendation/deepfm.py --input_dim 148 --n_fields 13 --embedding_dim 10 --fc_dims 10 5 1
  ```
  #### 2.2.3 prepare
  unzip `torch.zip` to `/angel-2.4.0-bin/bin/torch-lib`<br>
  move 'pytorch-on-angel-0.2.0.jar' 'pytorch-on-angel-0.2.0-jar-with-dependencies.jar' 'deepfm.pt' to `/angel-2.4.0-bin/bin/'<br>
  make dir for output
  ```
  hdfs dfs -mkdir /output
  ```
  upload files to hdfs
  ```
  hdfs dfs -put /usr/local/angel-2.4.0-bin/bin/ /
  hdfs dfs -put /usr/local/angel-2.4.0-bin/conf/ /
  hdfs dfs -put /usr/local/angel-2.4.0-bin/data/ /
  hdfs dfs -put /usr/local/angel-2.4.0-bin/lib/ /
  ```
  #### 2.2.4 run deepfm example
  ref：https://earlytobed.notion.site/Angel-2220b7d8f3f446a3aa4c6fc3f65c4c93<br>
  ```
  #!/bin/bash
  input=hdfs://h01:9000/data/census_148d_train.libsvn
  output=hdfs://h01:9000/output
  source ./spark-on-angel-env.sh
  JAVA_LIBRARY_PATH=pytorch-on-angel-0.2.0-jar-with-dependencies.jar:pytorch-on-angel-0.2.0.jar
  torchlib=torch-lib/libpthreadpool.a,torch-lib/libcpuinfo_internals.a,torch-lib/libCaffe2_perfkernels_avx2.a,torch-lib/libgmock.a,torch-lib/libprotoc.a,torch-lib/libnnpack.a,torch-lib/libgtest.a,torch-lib/libpytorch_qnnpack.a,torch-lib/libcaffe2_detectron_ops.so,torch-lib/libCaffe2_perfkernels_avx512.a,torch-lib/libgomp-753e6e92.so.1,torch-lib/libgloo.a,torch-lib/libonnx.a,torch-lib/libtorch_angel.so,torch-lib/libbenchmark_main.a,torch-lib/libcaffe2_protos.a,torch-lib/libgtest_main.a,torch-lib/libprotobuf-lite.a,torch-lib/libasmjit.a,torch-lib/libCaffe2_perfkernels_avx.a,torch-lib/libonnx_proto.a,torch-lib/libfoxi_loader.a,torch-lib/libfbgemm.a,torch-lib/libc10.so,torch-lib/libclog.a,torch-lib/libbenchmark.a,torch-lib/libgmock_main.a,torch-lib/libnnpack_reference_layers.a,torch-lib/libcaffe2_module_test_dynamic.so,torch-lib/libqnnpack.a,torch-lib/libprotobuf.a,torch-lib/libc10d.a,torch-lib/libtorch.so,torch-lib/libcpuinfo.a,torch-lib/libstdc++.so.6,torch-lib/libmkldnn.a

  spark-submit \
    --master yarn \
    --deploy-mode cluster \
    --conf spark.ps.instances=1 \
    --conf spark.ps.cores=1 \
    --conf spark.ps.jars=$SONA_ANGEL_JARS \
    --conf spark.ps.memory=4g \
    --conf spark.ps.log.level=INFO \
    --conf spark.driver.extraJavaOptions=-Djava.library.path=$JAVA_LIBRARY_PATH:. \
    --conf spark.executor.extraJavaOptions=-Djava.library.path=$JAVA_LIBRARY_PATH:. \
    --conf spark.executor.extraLibraryPath=. \
    --conf spark.driver.extraLibraryPath=. \
    --conf spark.executorEnv.OMP_NUM_THREADS=2 \
    --conf spark.executorEnv.MKL_NUM_THREADS=2 \
    --name "deepfm for torch on angel" \
    --jars $SONA_SPARK_JARS \
    --files deepfm.pt,$torchlib \
    --driver-memory 4g \
    --num-executors 1 \
    --executor-cores 1 \
    --executor-memory 4g \
    --class com.tencent.angel.pytorch.examples.supervised.RecommendationExample pytorch-on-angel-0.2.0.jar \
    trainInput:$input batchSize:128 torchModelPath:deepfm.pt \
    stepSize:0.001 numEpoch:10 testRatio:0.1 \
    angelModelOutputPath:$output

  ```

## Q&A
1. Q: torchvision version conflicts <br>
   A: change torchvision=0.4.2 in Dockerfile<br>
2. Q: spark submit always (state: ACCEPTED)<br>
   A: first, decreace spark-submit conf --driver-memory \ --num-executors \ --executor-cores \ --executor-memory \
      <br>
      then kill the application<br>
      ```
      yarn application -kill application_ID
      ```
      stop Hadoop in `/usr/local/hadoop/sbin`
      ```
      ./stop-all.sh
      ```
      clean files in `/home/hadoop2/hadoop/hdfs/name` and `/home/hadoop2/hadoop/hdfs/data`
      ```
      cd /home/hadoop2/hadoop/hdfs/name
      rm -r current
      cd /home/hadoop2/hadoop/hdfs/data
      rm -r dfs nm-local-dir
      ```
      format hadoop in `/usr/local/hadoop/bin`
      ```
      ./hadoop namenode -format
      ```
      restart hadoop in `/usr/local/hadoop/sbin`
      ```
      ./start-all.sh
      ```
3. Q: application finished with status:failed<br>
   A: check logs for more info
      ```
      yarn logs -applicationId xxxapplicationIdxxxx
      ```
