# TF-Encrypted

- [TF-Encrypted](https://tf-encrypted.io/)

## 简介

> 加密机器学习、联邦学习

TF Encrypted是TensorFlow中用于加密机器学习的框架。它看起来和感觉上都很像TensorFlow，利用了Keras API的易用性，同时通过安全的多方计算和同态加密实现了对加密数据的训练和预测。TF Encrypted的目的是让保护隐私的机器学习随时可用，而不需要密码学、分布式系统或高性能计算方面的专业知识。

## conda安装

```conda
conda create -n tfe python=3.6
conda activate tfe
conda install tensorflow notebook
pip install tf-encrypted
```

## 官方文档

官方文档给了三个案例

- 加密值的简单平均值
- 使用简单的神经网络对MNIST数据集进行私有预测。
- TF Encrypted为联邦学习执行安全聚合