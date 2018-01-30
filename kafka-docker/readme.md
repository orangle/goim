
```
docker run -p 2181:2181 -p 9092:9092 --env ADVERTISED_HOST=`docker-machine ip \`docker-machine active\`` --env ADVERTISED_PORT=9092  --hostname goim.test --name=goim.test -d spotify/kafka
```



create topic 
```
docker exec -i -t goim.test bash

cd /opt/kafka_2.11-0.10.1.0

bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic KafkaPushsTopic
```


启动所有服务

测试

```
浏览器打开  goim/examples/javascript 下面的demo.html文件

然后在命令行测试 

curl -d "{\"test\":23333}" "http://127.0.0.1:7172/1/push?uid=0"
```