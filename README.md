# ResNet50-NAM: 一种新的注意力计算方式复现

论文地址：https://arxiv.org/abs/2111.12419
## 简介
注意力机制在近年来大热，注意力机制可以帮助神经网络抑制通道中或者是空间中不太显著的特征。之前的很多的研究聚焦于如何通过注意力算子来获取显著性的特征。这些方法成功的发现了特征的不同维度之间的互信息量。但是，缺乏对权值的贡献因子的考虑，而这个贡献因子可以进一步的抑制不显著的特征。因此，我们瞄准了**利用权值的贡献因子来提升注意力**的效果。我们使用了**Batch Normalization**的缩放因子来表示权值的重要程度。这样可以**避免如SE，BAM和CBAM**一样增加全连接层和卷积层。这样，我们提出了一个新的注意力方式：基于归一化的注意力（**NAM**）。

## 方法
我们提出的NAM是一种轻量级的高效的注意力机制，我们采用了CBAM的模块集成方式，重新设计了通道注意力和空间注意力子模块，这样，NAM可以嵌入到每个网络block的最后。对于残差网络，可以嵌入到残差结构的最后。对于通道注意力子模块，我们使用了Batch Normalization中的缩放因子，如式子（1），缩放因子反映出各个通道的变化的大小，也表示了该通道的重要性。为什么这么说呢，可以这样理解，缩放因子即BN中的方差，方差越大表示该通道变化的越厉害，那么该通道中包含的信息会越丰富，重要性也越大，而那些变化不大的通道，信息单一，重要性小。
![](https://ai-studio-static-online.cdn.bcebos.com/b397bce30f4e4662bb68b7c5a975393773e5df9b1f404b22840330e22c82fae8)
![](https://ai-studio-static-online.cdn.bcebos.com/fbbd2c9dfc864689a2fb3a073eaa21bdcb9953ce89544162927185217e0aa2d8)

其中$\mu_B 和 \sigma_B$为均值，$B$为标准差，$\gamma 和 \beta$是可训练的仿射变换参数（尺度和位移）[参考Batch Normalization](https://arxiv.org/pdf/1502.03167.pdf).通道注意力子模块如图(1)和式(2)所示：
![](https://ai-studio-static-online.cdn.bcebos.com/86b757ab40f3460ba3430d7f7f71622d5d2c29622a5c4420921bfe033700b22b)
其中$M_c$表示最后得到的输出特征，$\gamma$是每个通道的缩放因子，因此，每个通道的权值可以通过 $W_\gamma =\gamma_i/\sum_{j=0}\gamma_j$ 得到。我们也使用一个缩放因子 $BN$ 来计算注意力权重，称为**像素归一化**。像素注意力如图(2)和式(3)所示：
![](https://ai-studio-static-online.cdn.bcebos.com/2b24da52d2614ebca2c387564fd8920d93289777228d460b9a81b0a8c4df152a)

为了抑制不重要的特征，作者在损失函数中加入了一个正则化项，如式(4)所示。




## 数据集介绍：Cifar100
链接：http://www.cs.toronto.edu/~kriz/cifar.html

![](https://ai-studio-static-online.cdn.bcebos.com/7baff8d84907478294ca778385fca688741d14db13d841c39f0125c41f0a0ac4)


CIFAR100数据集有100个类。每个类有600张大小为32 × 32 32\times 3232×32的彩色图像，其中500张作为训练集，100张作为测试集。