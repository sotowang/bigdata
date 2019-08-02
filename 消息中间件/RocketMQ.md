# 基本命令

* 启动nameServer

  > nohup ./mqnamesrv &

* 启动broker

  > nohup  sh mqbroker -n 127.0.0.1:9876 -c ../conf/2m-noslave/broker-a.properties > broker.out   &

* broker查看 

  > ./mqadmin clusterList -n 127.0.0.1:9876

