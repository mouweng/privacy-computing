# pysyft

- [pysyft安装教程](https://www.jianshu.com/p/42518e7189e3)
- [youtube官方教程](https://www.youtube.com/watch?v=DppXfA6C8L8)

## 简介

> Syft通过在一些深度学习框架（pytorch、tensorflow）中使用联邦学习、差分隐私和加密计算(如多方安全计算MPC和同态加密HE)将私有数据从模型训练中解耦。

你可以使用PySyft来执行两种类型的计算:

- 动态:直接计算你看不到的数据。
- 静态:创建静态的计算图，可以在稍后的时间在不同的计算上部署/缩放。

## 安装

```undefined
$ conda create -n pysyft python=3.9
$ conda activate pysyft
$ conda install jupyter notebook
$ pip install syft
```
