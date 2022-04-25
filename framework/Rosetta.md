# Rosetta

- [Rosetta官方教程](https://github.com/LatticeX-Foundation/Rosetta/blob/master/README_CN.md)
- [Rosetta部署指南](https://github.com/LatticeX-Foundation/Rosetta/blob/master/doc/DEPLOYMENT_CN.md)
- [bilibili视频教程](https://www.bilibili.com/video/BV1uC4y1t7FJ?spm_id_from=333.880.my_history.page.click)

## 简介

> Rosetta在现阶段首先以TensorFlow这一流行AI框架为基础，深度改造其前端Python入口和后端kernel实现，并封装可插拔的MPC算法协议作为“隐私保护引擎”来驱动整个计算过程中数据的安全流动。

- 以商用为目的，支持主流的AI框架，以TensorFlow为主
- 以AI工程师为目标人群，可以快速无门槛的从传统模式切换为隐私计算的模式
- 支持多种隐私计算技术，保持算法性能，并可按需进行配置



现版本Rosetta是只能支持三方的安全计算， SecureNN 和 Helix 是三方协议

中间传输的都是随机数，不是梯度；

## 调用

```python
import latticex.rosetta
```



## Rosetta框架核心设计思想

- **隐私算子（SecureOp）**作为核心抽象接口连接AI框架和隐私计算技术。TensorFlow在不同的层次上提供了多样的扩展方式，Rosetta选择后端算子（Operation）层作为核心切入点，TensorFlow在执行算子时会动态的绑定到具体MPC协议中的SecureOp实现中。通过这样的抽象，密码协议开发者可以不需要了解AI框架，只需要用C++实现满足接口定义的各个功能函数即可，而AI开发者也不需要深入了解MPC等技术的实现细节，而只需要在现有算子的基础上进一步封装自己想要的上层高级功能即可。

- 基于**优化遍（Pass）**的分阶段转换。为了尽可能的给AI开发者提供易用的接口，减少给线上AI程序赋予数据隐私保护能力时的改造成本，Rosetta在整体的设计中借鉴了程序编译器领域的核心概念：Pass。Pass是编译器中常用的技术，主要用作将源码文件一步步转变为机器码过程中的多轮转化和优化。在Rosetta中，用户使用原生TensorFlow接口编写的DAG（有向无环图）形式的逻辑计算图会被分阶段的转换、替换为多方协作执行的MPC程序，这样可以实现对于用户API层最少的改动。

具体的，在Rosetta中，有两个阶段的Pass，一个在前端Python层的全局DAG构建过程中生效的Static Pass，会将原生Tensor转换为支持自定义密文类型的RttTensor，将原生Operation 转换为支持tf.string格式输入输出的RttOp，并最终在图开始启动时进一步的转换为承载实际MPC操作的SecureOp。

另一个是在SecurOp的实际执行时所进行的Dynamic Pass处理，会动态的根据当前用户选择的协议选择对应的实际算子实现去执行，同时可以在此时嵌入基于执行上下文的优化处理。

