# 两个 Kafka 单节点实例

## 启动

`cd testdata/kafka.0.10.0.0 && docker-compose up -d`

## 操作单节点示例

消费:

```sh
$ export KT_BROKERS=127.0.0.1:9092 KT_VERSION=0.10.0.0
$ kt tail -topic greetings
2024/03/28 14:22:39 start to consume partition 0 in [-1, 9223372036854775807] / [Newest,1<<63-1]
#001 topic: greetings offset: 0 partition: 0 key:  timestamp: 2024-03-28 14:22:57.003 valueSize: 77B msg: {"value":"string: 新疆维吾尔自治区石河子市俕搯路3295号錼萈小区3单元939室","timestamp":"2024-03-28T14:22:57.003+08:00","offset":0,"partition":0}
#002 topic: greetings offset: 1 partition: 0 key:  timestamp: 2024-03-28 14:23:01.811 valueSize: 60B msg: {"value":"string: 四川省雅安市逼堆路2166号惠际小区18单元613室","timestamp":"2024-03-28T14:23:01.811+08:00","offset":1,"partition":0}
```

生产:

```sh
$ export KT_BROKERS=127.0.0.1:9092 KT_VERSION=0.10.0.0
$ ggt rand -t 地址 | kt produce -literal -topic greetings
{"count":1,"partition":0,"startOffset":1}
total messages 1, size 60B, cost: 200.419626ms, TPS: 4.989531 message/s 299B/s
```

## thanks

1. [Kafka入门与核心设计](http://blog.greetings.top/2022/03/14/2022-03-14-kafka%E5%85%A5%E9%97%A8%E4%B8%8E%E6%A0%B8%E5%BF%83%E8%AE%BE%E8%AE%A1/)
2. [kafka versions](https://endoflife.date/apache-kafka) Latest 3.7.0 (26 Feb 2024) 0.10.0.0 (22 May 2016)
3. [Version 3.0.1 Docs » Docker » Configuration](https://docs.huihoo.com/apache/kafka/confluent/3.0/cp-docker-images/docs/configuration.html#confluent-kafka-cp-kafka)
4. [docker hub confluentinc/cp-kafka:3.0.1](https://hub.docker.com/layers/confluentinc/cp-kafka/3.0.1/images/sha256-4ec612bb2e75408afb7414f4b64f0cae96fc64c612979bac44c6b0d55cf91ee9?context=explore)
   201.71 MB (7 years ago by samjhecht)

对于confluent版本和apache版本对照表如下：

| Confluent Platform | Apache Kafka® | Release Date       | Standard End of Support | Platinum End of Support |
|--------------------|---------------|--------------------|-------------------------|-------------------------|
| 7.0.x              | 3.0.x         | October 27, 2021   | October 27, 2023        | October 27, 2024        |
| 6.2.x              | 2.8.x         | June 8, 2021       | June 8, 2023            | June 8, 2024            |
| 6.1.x              | 2.7.x         | February 9, 2021   | February 9, 2023        | February 9, 2024        |
| 6.0.x              | 2.6.x         | September 24, 2020 | September 24, 2022      | September 24, 2023      |
| 5.5.x              | 2.5.x         | April 24, 2020     | April 24, 2022          | April 24, 2023          |
| 5.4.x              | 2.4.x         | January 10, 2020   | January 10, 2022        | January 10, 2023        |
| 5.3.x              | 2.3.x         | July 19, 2019      | July 19, 2021           | July 19, 2022           |
| 5.2.x              | 2.2.x         | March 28, 2019     | March 28, 2021          | March 28, 2022          |
| 5.1.x              | 2.1.x         | December 14, 2018  | December 14, 2020       | December 14, 2021       |
| 5.0.x              | 2.0.x         | July 31, 2018      | July 31, 2020           | July 31, 2021           |
| 4.1.x              | 1.1.x         | April 16, 2018     | April 16, 2020          | April 16, 2021          |
| 4.0.x              | 1.0.x         | November 28, 2017  | November 28, 2019       | November 28, 2020       |
| 3.3.x              | 0.11.0.x      | August 1, 2017     | August 1, 2019          | August 1, 2020          |
| 3.2.x              | 0.10.2.x      | March 2, 2017      | March 2, 2019           | March 2, 2020           |
| 3.1.x              | 0.10.1.x      | November 15, 2016  | November 15, 2018       | November 15, 2019       |
| 3.0.x              | 0.10.0.x      | May 24, 2016       | May 24, 2018            | May 24, 2019            |
| 2.0.x              | 0.9.0.x       | December 7, 2015   | December 7, 2017        | December 7, 2018        |
| 1.0.0              | –             | February 25, 2015  | February 25, 2017       | February 25, 2018       |

### 创建Topic

默认自动创建Topic配置是启用的，也就是说第一步也可以省略，只要有Producer或者Consumer,就会自动创建Topic的。

`docker-compose exec broker1 kafka-topics --bootstrap-server broker:9092 --create --topic greetings`

### 查看 Topic 信息

`docker-compose exec broker1 kafka-topics --describe --zookeeper zookeeper1:2181 --topic greetings`

```sh
Topic:greetings PartitionCount:1        ReplicationFactor:1     Configs:
        Topic: greetings        Partition: 0    Leader: 1       Replicas: 1     Isr: 1
```

### 生产消息

`docker-compose exec --interactive --tty broker1 kafka-console-producer --broker-list broker1:9092 --topic greetings`

然后输入测试信息，完成后执行Ctrl-D可以退出命令行。

### 消费消息

`docker-compose exec --interactive --tty broker1 kafka-console-consumer --bootstrap-server broker1:9092 --zookeeper zookeeper1:2181 --topic greetings --from-beginning`

执行Ctrl-C可以退出命令行

以上例子中 kafka-topics kafka-console-producer 和 kafka-console-consumer 具体参数和用法详见文档。

###   