# **PaddleFL**

- [PaddleFL官方教程](https://github.com/PaddlePaddle/PaddleFL/blob/master/README_cn.md)

## 简介

> PaddleFL 基于 PaddlePaddle 框架对联邦学习进行扩展实现。PaddleFL也提供了在自然语言处理，计算机视觉和推荐算法等领域的应用示例。PaddleFL支持当前主流两类联邦学习策略：横向联邦学习策略和纵向联邦学习策略。

PaddleFL 基于 PaddlePaddle 框架对联邦学习进行扩展实现。PaddleFL也提供了在自然语言处理，计算机视觉和推荐算法等领域的应用示例。PaddleFL支持当前主流两类联邦学习策略：横向联邦学习策略和纵向联邦学习策略。

未来会对联邦学习中的多任务学习以及迁移学习等进行开发和支持。

- **横向联邦学习策略:** 联邦平均 ，差分隐私 ，安全聚合；
- **纵向联邦学习策略:** 基于privc的两方训练，基于ABY3的三方训练；



## PaddleFL框架设计

PaddleFL 中主要提供**两种解决方案**：Data Parallel 以及 Federated Learning with MPC (PFM)。

（1）通过Data Parallel，各数据方可以基于经典的横向联邦学习策略（如 FedAvg，DPSGD等）完成模型训练。

（2）PFM是基于多方安全计算（MPC）实现的联邦学习方案。作为PaddleFL的一个重要组成部分，PFM可以很好地支持联邦学习，包括横向、纵向及联邦迁移学习等多个场景。既提供了可靠的安全性，也拥有可观的性能。

- ### **Data Parallel**

在PaddeFL中，模型训练的整体流程主要分为两个阶段：编译阶段和运行阶段。编译阶段主要**定义联邦学习任务**，运行阶段主要进行**联邦学习训练工作**，每个阶段主要包含的组件如下：



#### **A. 编译阶段**

- FL-Strategy: 用户可以使用FL-Strategy定义联邦学习策略，例如Fed-Avg[2]。
- User-Defined-Program: PaddlePaddle的程序定义了机器学习模型结构和训练策略，如多任务学习。
- Distributed-Config: 在联邦学习中，系统会部署在分布式环境中。分布式训练配置定义分布式训练节点信息。
- FL-Job-Generator: 给定FL-Strategy, User-Defined Program 和 Distributed Training Config，联邦参数的Server端和Worker端的FL-Job将通过FL Job Generator生成。FL-Jobs 被发送到组织和联邦参数服务器以进行联合训练。

#### **B. 运行阶段**

- FL-Server: 在云或第三方集群中运行的联邦参数服务器。
- FL-Worker: 参与联合学习的每个组织都将有一个或多个与联合参数服务器通信的Worker。
- FL-Scheduler: 训练过程中起到调度Worker的作用，在每个更新周期前，决定哪些Worker可以参与训练。

- ### **Federated Learning with MPC**

PaddleFL MPC 中的安全训练和推理任务是基于高效的多方计算协议实现的，PaddleFL支持三方安全计算协议ABY3和两方计算协议PrivC。基于PrivC的两方联邦学习主要支持线性/逻辑回归、DNN模型。基于ABY3的三方联邦学习线性/逻辑回归、DNN、CNN、FM等

在PaddleFL MPC中，参与方可分为：输入方、计算方和结果方。输入方为训练数据及模型的持有方，负责加密数据和模型，并将其发送到计算方（ABY3协议使用三个计算节点、PrivC协议使用两个计算节点）。计算方为训练的执行方，基于特定的多方安全计算协议完成训练任务。计算方只能得到加密后的数据及模型，以保证数据隐私。计算结束后，结果方会拿到计算结果并恢复出明文数据。每个参与方可充当多个角色，如一个数据拥有方也可以作为计算方参与训练。



PFM的整个训练及推理过程主要由三个部分组成：数据准备，训练/推理，结果解析。

#### **A. 数据准备**

- 私有数据对齐： PFM允许数据拥有方（数据方）在不泄露自己数据的情况下，找出多方共有的样本集合。此功能在纵向联邦学习中非常必要，因为其要求多个数据方在训练前进行数据对齐，并且保护用户的数据隐私。
- 数据加密及分发：在PFM中，提供在线或离线两种数据加密及分发方案。如果采用离线分发数据，那么数据方在数据准备阶段将数据和模型用秘密共享[9]的方法加密，然后用直接传输或者数据库存储的方式传到计算方。如果选择在线分发数据，数据方在训练过程中在线地对数据和模型进行加密和分发。在数据加密及分发过程中，每个计算方只会拿到数据的一部分，因此计算方无法还原真实数据。

#### **B. 训练/推理**

PFM 拥有与PaddlePaddle相同的运行模式。在训练前，用户需要定义MPC协议，训练模型以及训练策略。paddle_fl.mpc 中提供了可以操作加密数据的算子，在运行时算子的实例会被创建并被执行器依次运行（训练过程中密文的通信支持gloo和grpc两种网络通信模式）。

#### **C. 结果重构**

安全训练和推理工作完成后，模型（或预测结果）将由计算方以加密形式输出。结果方可以收集加密的结果，使用PFM中的工具对其进行解密，并将明文结果传递给用户（目前数据的分片和重构支持离线和在线两种模式）。



## 安装

### 环境依赖

- CentOS 7 (64 bit)
- Python 3.5/3.6/3.7/3.8 ( 64 bit)
- pip3 9.0.1+ (64 bit)
- PaddlePaddle 1.8.5
- Redis 5.0.8 (64 bit)
- GCC or G++ 8.3.1
- cmake 3.15+

### 安装部署

我们提供三种方式安装PaddleFL，您可以根据自己的实际情况进行选择：

- 在Docker中使用PaddleFL


我们**强烈建议** 您在docker中使用PaddleFL。

```
#Pull and run the docker
docker pull paddlepaddle/paddlefl:1.1.2
docker run --name <docker_name> --net=host -it -v $PWD:/paddle <image id> /bin/bash
```

Docker中环境配置以及paddlepaddle和paddlefl已经安装完成，可以直接运行示例代码，开始使用PaddleFL。

