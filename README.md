[![build status](https://travis-ci.com/alibaba/canal.svg?branch=master)](https://travis-ci.com/alibaba/canal)
[![codecov](https://codecov.io/gh/alibaba/canal/branch/master/graph/badge.svg)](https://codecov.io/gh/alibaba/canal)
![maven](https://img.shields.io/maven-central/v/com.alibaba.otter/canal.svg)
![license](https://img.shields.io/github/license/alibaba/canal.svg)
[![average time to resolve an issue](http://isitmaintained.com/badge/resolution/alibaba/canal.svg)](http://isitmaintained.com/project/alibaba/canal "average time to resolve an issue")
[![percentage of issues still open](http://isitmaintained.com/badge/open/alibaba/canal.svg)](http://isitmaintained.com/project/alibaba/canal "percentage of issues still open")

## 背景

早期，阿里巴巴 B2B 公司因为存在杭州和美国双机房部署，存在跨机房同步的业务需求 ，主要是基于trigger的方式获取增量变更。从 2010 年开始，公司开始逐步尝试数据库日志解析，获取增量变更进行同步，由此衍生出了增量订阅和消费业务，从此开启一段新纪元。

当前的 canal 支持源端 MySQL 版本包括 5.1.x , 5.5.x , 5.6.x , 5.7.x , 8.0.x

基于日志增量订阅和消费的业务包括
- 数据库镜像
- 数据库实时备份
- 索引构建和实时维护(拆分异构索引、倒排索引等)
- 业务 cache 刷新
- 带业务逻辑的增量数据处理

## 项目介绍

#### 名称：canal [kə'næl]
#### 译意： 水道/管道/沟渠
#### 产品定位： 基于数据库增量日志解析，提供增量数据订阅和消费
#### 关键词： MySQL binlog parser / real-time / queue&topic / index build

## 工作原理

#### MySQL主备复制原理
![](http://dl.iteye.com/upload/attachment/0080/3086/468c1a14-e7ad-3290-9d3d-44ac501a7227.jpg)

- MySQL master 将数据变更写入二进制日志( binary log, 其中记录叫做二进制日志事件binary log events，可以通过 show binlog events 进行查看)
- MySQL slave 将 master 的 binary log events 拷贝到它的中继日志(relay log)
- MySQL slave 重放 relay log 中事件，将数据变更反映它自己的数据

#### canal 工作原理
![](http://dl.iteye.com/upload/attachment/0080/3107/c87b67ba-394c-3086-9577-9db05be04c95.jpg)

- canal 模拟 MySQL slave 的交互协议，伪装自己为 MySQL slave ，向 MySQL master 发送dump 协议
- MySQL master 收到 dump 请求，开始推送 binary log 给 slave (即 canal )
- canal 解析 binary log 对象(原始为 byte 流)

## 重要版本更新说明

canal 1.1.x 版本（[release_note](https://github.com/alibaba/canal/releases)）,性能与功能层面有较大的突破,重要提升包括:

- 整体性能测试&优化,提升了150%. #726 参考: [Performance](https://github.com/alibaba/canal/wiki/Performance)
- 原生支持prometheus监控 #765 [Prometheus QuickStart](https://github.com/alibaba/canal/wiki/Prometheus-QuickStart)
- 原生支持kafka消息投递 #695 [Canal Kafka/RocketMQ QuickStart](https://github.com/alibaba/canal/wiki/Canal-Kafka-RocketMQ-QuickStart)
- 原生支持aliyun rds的binlog订阅 (解决自动主备切换/oss binlog离线解析) 参考: [Aliyun RDS QuickStart](https://github.com/alibaba/canal/wiki/aliyun-RDS-QuickStart)
- 原生支持docker镜像 #801 参考: [Docker QuickStart](https://github.com/alibaba/canal/wiki/Docker-QuickStart)

## 文档

- [Home](https://github.com/alibaba/canal/wiki/Home)
- [Introduction](https://github.com/alibaba/canal/wiki/Introduction)
- [QuickStart](https://github.com/alibaba/canal/wiki/QuickStart)
  - [Docker QuickStart](https://github.com/alibaba/canal/wiki/Docker-QuickStart)
  - [Canal Kafka/RocketMQ QuickStart](https://github.com/alibaba/canal/wiki/Canal-Kafka-RocketMQ-QuickStart")
  - [Aliyun RDS for MySQL QuickStart](https://github.com/alibaba/canal/wiki/aliyun-RDS-QuickStart)
  - [Prometheus QuickStart](https://github.com/alibaba/canal/wiki/Prometheus-QuickStart)
- [AdminGuide](https://github.com/alibaba/canal/wiki/AdminGuide)
- [ClientExample](https://github.com/alibaba/canal/wiki/ClientExample)
- [ClientAPI](https://github.com/alibaba/canal/wiki/ClientAPI)
- [Performance](https://github.com/alibaba/canal/wiki/Performance)
- [DevGuide](https://github.com/alibaba/canal/wiki/DevGuide)
- [BinlogChange(MySQL 5.6)](https://github.com/alibaba/canal/wiki/BinlogChange%28mysql5.6%29)
- [BinlogChange(MariaDB)](https://github.com/alibaba/canal/wiki/BinlogChange%28MariaDB%29)
- [TableMetaTSDB](https://github.com/alibaba/canal/wiki/TableMetaTSDB)
- [ReleaseNotes](http://alibaba.github.com/canal/release.html)
- [Download](https://github.com/alibaba/canal/releases)
- [FAQ](https://github.com/alibaba/canal/wiki/FAQ)

## 多语言

canal 特别设计了 client-server 模式，交互协议使用 protobuf 3.0 , client 端可采用不同语言实现不同的消费逻辑，欢迎大家提交 pull request 
  
- canal java 客户端: [https://github.com/alibaba/canal/wiki/ClientExample](https://github.com/alibaba/canal/wiki/ClientExample)
- canal c# 客户端: [https://github.com/dotnetcore/CanalSharp](https://github.com/dotnetcore/CanalSharp)
- canal go客户端: [https://github.com/CanalClient/canal-go](https://github.com/CanalClient/canal-go)
- canal php客户端: [https://github.com/xingwenge/canal-php](https://github.com/xingwenge/canal-php)

canal 作为 MySQL binlog 增量获取和解析工具，可将变更记录投递到 MQ 系统中，比如 Kafka/RocketMQ，可以借助于 MQ 的多语言能力 

- 参考文档: [Canal Kafka/RocketMQ QuickStart](https://github.com/alibaba/canal/wiki/Canal-Kafka-RocketMQ-QuickStart)

## 相关开源

- [canal 消费端开源项目: Otter](http://github.com/alibaba/otter">http://github.com/alibaba/otter)
- [阿里巴巴去 Oracle 数据迁移同步工具:yugong](http://github.com/alibaba/yugong">http://github.com/alibaba/yugong)

## 问题反馈
- qq交流群: 161559791
- 邮件交流: jianghang115@gmail.com
- 新浪微博: agapple0002
- 报告 issue: [github issues](https://github.com/alibaba/canal/issues)

## 其他资料

  - [ ADC 阿里技术嘉年华分享 ppt ( google docs)](https://docs.google.com/presentation/d/1MkszUPYRDkfVPz9IqOT1LLT5d9tuwde_WC8GZvjaDRg/edit?usp=sharing) 
