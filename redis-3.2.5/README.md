
一、配置环境
    
    CentOS V7.4
    Ruby V2.5.5
    RubyGems V3.0.4
    Redis V3.2.5
    
二、安装运行环境

   将tar目录下安装包上传至/usr/local/src
   
   1、安装Ruby(https://www.ruby-lang.org/en/downloads/)
      
      解压：tar -zxvf ruby-2.5.5.tar.gz
      安装：cd ruby-2.5.5
           ./configure
           make && make install
      查看：ruby -v
    
   2、安装RubyGems(https://rubygems.org/pages/download)
   
      解压：tar -zxvf rubygems-3.0.4.tgz
      安装：cd rubygems-3.0.4
           ruby setup.rb
      查看：gem -v
      
   3、安装ruby-redis接口
   
      命令：gem install redis
      
三、搭建redis集群
   
   1、安装redis
    
      解压：tar -zxvf redis-3.2.5.tar.gz
           mv redis-3.2.5 /usr/local/redis
      安装：cd /usr/local/redis
           make
           cp src/redis-cli /usr/local/bin/
           cp src/redis-server /usr/local/bin/
           cp src/redis-trib.rb /usr/local/bin/
           
   2、创建单机6个nodes
   
     mkdir -p /usr/local/redis/redis-cluster/7000
     mkdir -p /usr/local/redis/redis-cluster/7001
     mkdir -p /usr/local/redis/redis-cluster/7002
     mkdir -p /usr/local/redis/redis-cluster/7003
     mkdir -p /usr/local/redis/redis-cluster/7004
     mkdir -p /usr/local/redis/redis-cluster/7005
     
     cp /usr/local/redis/redis.conf /usr/local/redis/redis-cluster/7000
     cp /usr/local/redis/redis.conf /usr/local/redis/redis-cluster/7001
     cp /usr/local/redis/redis.conf /usr/local/redis/redis-cluster/7002
     cp /usr/local/redis/redis.conf /usr/local/redis/redis-cluster/7003
     cp /usr/local/redis/redis.conf /usr/local/redis/redis-cluster/7004
     cp /usr/local/redis/redis.conf /usr/local/redis/redis-cluster/7005
     
     修改 /usr/local/redis/redis-cluster/700*/redis.conf 配置信息：
     port 700*
     daemonize yes
     bind ip地址
     cluster-enabled yes
     cluster-config-file /usr/local/redis/redis-cluster/700*/nodes-700*.conf
     appendonly yes
     dir /usr/local/redis/redis-cluster/700*/
     
   3、启动所有redis
   
      redis-server /usr/local/redis/redis-cluster/7000/redis.conf
      redis-server /usr/local/redis/redis-cluster/7001/redis.conf
      redis-server /usr/local/redis/redis-cluster/7002/redis.conf
      redis-server /usr/local/redis/redis-cluster/7003/redis.conf
      redis-server /usr/local/redis/redis-cluster/7004/redis.conf
      redis-server /usr/local/redis/redis-cluster/7005/redis.conf
      
      ps -ef|grep redis
      
   4、创建集群
     
      redis-trib.rb create --replicas 1 172.16.7.103:7000 172.16.7.103:7001 172.16.7.103:7002 172.16.7.103:7003 172.16.7.103:7004 172.16.7.103:7005
    
   5、查看集群状态
      
      登录：redis-cli -c -h 172.16.7.103 -p 7000
      查看：cluster nodes
           cluster info
      