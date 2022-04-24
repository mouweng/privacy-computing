# GLF

- [Galaxy learning doc](https://galaxylearning.github.io/)
- [github GFL](https://github.com/GalaxyLearning/GFL)
- [bilibili演示](https://www.bilibili.com/video/BV12E411H7Lk?spm_id_from=333.337.search-card.all.click)

## 简介

> 由浙江大学吴超老师领导的团队所开发的一款基于Pytorch的联邦学习开源框架

**Galaxy Federated Learning Framework(GFL)** 是一个基于区块链的去中心化联邦学习框架。GFL以Ethereum为基础建立去中心化通信网络，通过智能合约完成联邦学习中要求可信的关键功能。同时，GFL简化了联邦学习建模过程，让开发者可以快速建模和验证。

### 优势

1. 集成了传统的**FedAvg联邦学习算法**和基于**知识蒸馏**的联邦学习算法。
2. 提供基于区块链的通信支持，结合知识蒸馏算法，为用户提供去中心化连版本学习服务。

## 迭代

从PFL到GFL

## GFL基础设计

![](https://cdn.jsdelivr.net/gh/mouweng/FigureBed/img/202204241029711.png)

GFL框架分为两部分：

- **Job Generator**

用于创建可以在GFL网络中执行的Job。开发者可以通过GFL提供的接口， 将各项配置参数生成一个Job分发到网络中以待训练。

- **Run-Time Network**

若干运行中的节点组成了GFL的去中心化训练网络， 每个GFL节点同时也是区块链中的一个节点。这些节点根据用户命令不停的处理网络中待训练的Job。

## GFL核心框架结构

- **Manager Layer**
  - 节点的启动/停止/状态探测
  - 提供节点的进程间通信接口
  - 同步Job
- **Scheduler Layer**
  - 管理每个Job的执行流程
  - 在节点间同步参数文件
  - 调度节点上的多Job执行顺序
- **FL Layer**
  - 配置Job的运行环境
  - 执行训练/聚合任务
  - 提供用户自定义动作接口