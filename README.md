# start-on-Angel
steps to get started with Tencent project—Angel<br>
the whole project is built in docker, Ubuntu

## dependencies
1. docker 
2. java 1.8
3. maven 3.3.9
4. scala 2.13.0
5. protobuf 2.5
6. hadoop 2.7.1
7. hbase 1.4.13
8. spark 2.3.0
9. Angel 2.4.0
10. pytorch on Angel 0.2.0
11. pytorch 1.3.1
12. torchvision 0.4.2

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


## step2：build Angel and pytorch-on-Angel

## step3：run examples
ref：https://earlytobed.notion.site/Angel-2220b7d8f3f446a3aa4c6fc3f65c4c93

## Q&A
1.
