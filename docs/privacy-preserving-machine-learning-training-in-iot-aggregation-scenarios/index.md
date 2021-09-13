# 论文笔记：Privacy-Preserving Machine Learning Training in IoT Aggregation Scenarios


*[Liehuang Zhu](https://arxiv.org/search/cs?searchtype=author&query=Zhu%2C+L), [Xiangyun Tang](https://arxiv.org/search/cs?searchtype=author&query=Tang%2C+X), [Meng Shen](https://arxiv.org/search/cs?searchtype=author&query=Shen%2C+M), [Jie Zhang](https://arxiv.org/search/cs?searchtype=author&query=Zhang%2C+J), [Xiaojiang Du](https://arxiv.org/search/cs?searchtype=author&query=Du%2C+X)*

*IEEE Internet of Things Journal*

https://arxiv.org/abs/2009.09691



> **摘要**：在智能城市的开发中，机器学习(ML)越来越受欢迎，它欣赏从各种物联网(IoT)设备生成的高质量训练数据集，这自然引发了人们对此类设置中可以提供的隐私保障的质疑。聚合场景中的隐私保护ML训练使模型需求者能够使用从物联网设备收集的敏感物联网数据安全地训练ML模型。现有的解决方案一般都是服务器辅助的，无法应对服务器之间或服务器与数据所有者之间的共谋威胁，与物联网的微妙环境不匹配。提出了一种基于部分同态加密的构建块库的隐私保护ML训练框架HEDA，该框架能够在不依赖不可信服务器的情况下，针对聚合场景构建多个隐私保护的ML训练协议，并在合谋情况下维护安全性。严格的安全性分析表明，所提出的协议能够保护诚实但好奇模型中每个参与者的隐私，并保证大多数合谋情况下的安全性。大量实验验证了HEDA算法的有效性，在保证模型精度的前提下实现了隐私保护的ML训练。

**本文贡献：**

1. 为了在密文上训练ML模型而没有精度损失，我们设计了一个基于加法同构加密Paillier[42]和乘法同构加密CloudRSA[43]的构建块库。受排列和组合思想的启发，复杂算法可以分解为几个原始操作，在确定了许多ML训练算法的基础上的一组核心操作之后，我们仔细设计了一个支持每个核心操作的构建块库。
2. 为了保证合谋情况下的安全性和满足聚合场景下的安全需求，我们设计了另外两个构件，可以将一个算法的输出转换为另一个算法的输入。所有构建块都以可组合的方式设计，并且同时满足功能性和安全性，其中构建块之间的组合的安全性通过模块顺序组合来确保[44]。
3. 我们实例化了三个隐私保护的ML训练协议：a)LR；b)SVM；c)NB，没有不可信服务器的帮助。据我们所知，我们是第一个在没有任何近似方程的情况下求解隐私保护LR训练中的非线性函数的。



## RELATED WORK

![](https://images.yingwai.top/picgo/202109100922640.png)



## SYSTEM OVERVIEW

### System Model

作者设想了一个数据驱动的物联网生态系统，如图1所示，包括物联网设备、物联网数据所有者和模型需求者。

1. *物联网设备*：它们负责通过无线或有线网络感知和传输有价值的物联网数据。
2. *数据所有者*：他们从自己域内的物联网设备收集所有物联网数据。
3. *模型需求者*：它想要在从多个数据所有者收集的数据集上训练ML模型。

![](https://images.yingwai.top/picgo/202109131015911.png)

物联网设备能够通过4G和WiFi等无线网络感知和传输有价值的物联网数据。这些物联网数据涵盖了智能城市中的各种现实应用，从环境变化到生理状况和个人信息。物联网数据所有者从自己域内的物联网设备收集所有数据。在域中存储和收集物联网数据后，物联网设备将在执行Heda协议期间离线，不再参与模型培训。数据所有者接管物联网设备的后续行为。

本文假设所有参与者都同意Heda去联合训练一个模型，并且每个数据所有者都同意将全局模型发布到服务器。每个数据所有者生成其个人密钥对来加密其数据，模型需求者也持有自己的密钥对用于模型加密。

### Threat Model

每个数据所有者可以通过诚实地执行预先设置的协议，尽可能多地了解其他数据所有者的敏感数据和模型需求者的模型。模型需求者诚实地遵守协议，但它试图从他了解的值中尽可能多地推断数据所有者的敏感数据。因此，对于任何参与者，本文都假设它是被动的(或诚实但好奇的)对手[53]，也就是说，它确实遵循协议，但它试图从他们学习的价值观中尽可能多地推断其他参与者的隐私。

所提出的构建块（安全求和除外）处于双方计算设置中，应在诚实但好奇的模型中确保安全。虽然由构建块构建的私有保护ML训练协议是多方协议，其中参与者可以彼此串通以获取更多信息以推断其他参与者的隐私。在本文的解决方案中，最多允许 $(n-1)$ 个数据所有者相互勾结以泄露其他参与者的隐私，最多允许 $(n - 2)$ 数据所有者最多与模型需求者勾结以泄露其他参与者的隐私。请注意，当数据所有者彼此勾结时，他们可以在其联合数据集上计算模型结果。因此，抵御这种极端情况毫无意义。

本文的模型可能会遇到外部攻击者，他们在传输过程中通过互联网窃听或其他方式非法获取数据。而外部对手可以通过使用现有的通用技术（如TSL）建立保密和可信的通道来控制。



## BUILDINGBLOCKS BASED ON PARTIAL HOMOMORPHIC ENCRYPTION

### Design Idea

类似数学公式可以分解为加减乘除的排列组合，ML 训练算法也可以分解为一组核心基本运算。本文就是基于这样的思路设计了一系列安全的计算方法。



### Building Blocks for Primitive Operations

![](https://images.yingwai.top/picgo/202109131046063.png)

#### Building Block 1-4

由于很容易获得安全的加法、减法和乘法，因此本文将它们直接包含在构建块库中。

1. Pillier 安全加法
2. Pillier 安全减法
3. Pillier 明文-密文乘法
4. Cloud-RSA 密文乘法

根据 Pillier 以及 Cloud-RSA 方案的安全性很容易证明 building blocks 1-4 在半诚实设置下是安全的。

#### Building Block 5 (Secure Power Function)

假设数据所有者使用他的 Cloud-RSA 公钥加密 $e^\bold{a}$，并将密文 $[e^\bold{a}]_R$ 发送给模型需求者。模型需求者有 $\bold{b} = \{b_1, b_2, ..., b_d\}$，希望获得 $[e^{\bold{a \cdot b}}]_R$：

<div>$$\begin{align}
[e^{\textbf{a} \cdot \textbf{b}}]_R &= [e^{a_1b_1 + a_2b_2 + \cdots +a_db_d}]_R \\
&= \prod^d_{i=1}[e^{a_ib_i}]_R = \prod^d_{i=1}([e^{a_i}]_R)^{b_i} \tag{1}
\end{align}$$</div>

公式(1)定义了本文的安全幂函数。通过这种方式，模型需求者可以通过 $(\sum^d_{i=1} b_i + d - 1)$ 次乘法获得 $[e^{\bold{a \cdot b}}]_R$。

#### Building Block 6 (Secure Summation)

有 $n$ 个数据拥有者，本文的安全聚合方法可以保证在不泄露各个数据拥有者隐私的情况以及各个数据拥有者密钥不同的情况下完成聚合：

![](https://images.yingwai.top/picgo/202109131447297.png)



### Building Blocks for Conversion

由于Heda涉及两个不同的密码系统和多个参与者，使用上述构建块是不够的。本文开发了两个协议，用于将密文从一种加密方案转换为另一种加密方案，同时保持底层明文。

#### Building Block 7 (Converting Cloud-RSA to Paillier ($[e^{\bold{a \cdot b}}]_R$ to $[e^{\bold{a \cdot b}}]_P$))

![](https://images.yingwai.top/picgo/202109131508251.png)

#### Building Block 8 (Converting one Paillier to Another Paillier ($[m]^1_P$ to $[m]^2_P$))

在安全聚合中，由于各个数据拥有者的密钥都不一样，因此还需要将用某个数据拥有者密钥加密的密文转换为用另一个数据拥有者密钥加密的密文。

![](https://images.yingwai.top/picgo/202109131508328.png)



## PRIVACY-PRESERVING MACHINE LEARNING TRAINING WITH HEDA

在这一部分中，将详细介绍如何使用所提出的构建块来构建一个保护隐私的ML训练协议来训练LR模型。受版面限制，本文在完整版[16]中给出了用于训练SVM(协议1)和NB(协议3)的隐私保护ML协议的构造。

![](https://images.yingwai.top/picgo/202109131513152.png)


