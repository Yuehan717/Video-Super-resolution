# 【论文笔记】Image Super-Resolution Using Very Deep Residual Channel Attention Networks
对于CNN网络来说，网络深度非常重要。但是随着网络深度增加，训练难度往往也会上升。这篇论文提出了一种方法，
可以搭建非常深的SISR模型，效果也有比较大的提升。论文关注的另外一个问题是，低分辨率的输入图像和他们的
feature map里面包含丰富的low-frequency信息，而模型平等处理这些来自不同通道的特征，这会阻碍网络的表示能力。
RCAN提出了一种通道注意力机制（channel attention）来解决这个问题。
## 主要贡献
论文提出了一个可以完成高精度图像超分的深度网络（RCAN）。
+ Proposed residual in residual structure to construct very deep trainable networks.
+ Propoesd channel attention (CA) mechanism to adaptively rescale features by considering interdependencies
among feature channels.
## 模型
### 网络架构
<img src=https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/RCAN.png width=80%> </img>  
RCAN包含四个部分：**shallow feature extraction, RIR, upsample, reconstruction**. 第一部分特征提取和最后的重建
都由一个卷积层构成，upsample可以选择的方法有很多，论文当中没有明确给出，需要看作者给的代码实现。RCAN尽量将处理任务放在RIR
中，简化模型的其他部分。  
+ RIR组成：G个RG（Residual Group）+ conv，带LSC。
+ RG组成：B个RCAB + conv，带SSC。
+ RCAB组成：conv + ReLU + conv + CA (channel attention)
+ CA组成：Global Pooling + conv + ReLU + conv + sigmoid gate
### Residual in Residual (RIR)
RIR结构由G个**residual group (RG)** 组成。RIR的首尾由long skip connection（LSC）连接。LSC可以减轻模型向前传递低频信息的负担，同时让RIR能学习到粗略的残差信息。  
每一个RG有B个RCAB和末尾的一个卷积层组成。RG的首尾由short skip connection（SSC）连接。SSC让网络的主要部分关注残差信息。  
LSC和SSC整体上在模型中增加了identity-based skip connection。作者这样做的原因是，LR图像所包含的低频信息最终会出现在HR图像中，
这些信息非常丰富并且实际上不需要进行处理，增加skip connection可以直接传递这些信息，而网络的主要部分专注于学习残差信息就可以。这样的设计给网络训练减少了很多负担，也让模型的学习更有效。
### Channel Attention (CA)
<img src=https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/CA.png width=60%></img>  
注意力机制经常被用在有序信息的学习和一些high-level的视觉任务当中，作者第一次把这种方法用在low-level的SR任务里面。
如何为每一个feature channel生成一个attention是关键步骤。作者最后给出了一个使用global average pooling计算attention的方法，
并且给出了这样设计的依据，原文如下，还没太理解这之间的关系。
> First, information in the LR space has abundant low-frequency and valuable high-frequency components. The low-frequency parts seem to be more complanate. 
The high-frequency components would usually be regions, being full of edges, texture, and other details. On the other hand, each filter in Conv layer operates 
with a local receptive field. Consequently, the output after convolution is unable to exploit contextual information outside of the local region.

图中的HGP可以看作一个global polling层，WD是一个进行channel-downscaling的卷积层，WD产生的特征图经过一个ReLU函数的处理
输入到WU中，WU也是一个卷积层，负责channel-upscaling，最后的f是sigmoid函数。经过这一系列的处理得到了s，s和最开始的输入相乘，对输入的各个通道进行rescale。
> WD和WU进行的down-scaling和upscaling目的是什么？

### Residual Channel Attention Block (RCAB)
<img src=https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/RCAB.png width=60%></img>  
图中虚线框内就是上一部分所讲的channel attention，除此之外RCAB首先用两个卷积层对输入进行处理，然后在处理过的特征图上提取channel statistic，左后获得的这个值
和整个block的输入通过一个short cut连接起来。  
和EDSR进行对比，可以把EDSR看作是RCAN的一个特例，如果channel statistic是常数0.1， 那么RCAB中的channel statistic就相当于EDSR中每个residual block最后的scaling层。
用channel attention替代scaling层考虑了各个通道之间的相互关系。
> 原文：Although the channel-wise feature rescaling is introduced to train a very wide network, the interdependencies among channels are not considered in EDSR.
但是这里的*interdependencies*具体该怎么理解？

作者对**CA**的灵感来源于[Squeeze-and-Excitation Networks](https://openaccess.thecvf.com/content_cvpr_2018/html/Hu_Squeeze-and-Excitation_Networks_CVPR_2018_paper.html)，这篇文章
对CA的机制有比较详细的讲解，前面提出的问题也基本能在这篇文章里找到答案。作者很好地把这篇文章里的机制运用在了SR任务上。
### Sqeeze and Excitation Networks
卷积神经网络（CNN）的核心是卷积运算，它使网络能够通过在每一层的局部感受野(receptive field)内融合空间和通道信息来构造信息特征。已经有很多研究探讨了空间信息的问题，这篇论文考虑通道间的关系。总的来说，SENet想要使得包含更重要特征的通道获得更多计算资源，同时，在考虑什么是”更重要的特征通道“时，SENet考虑到了通道之间的依赖关系。  
<img src=https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/SEB.png width=80%></img>  
SENet中最重要的是SE块（Squeeze-and-Excitation Block），它完成了根据不同通道的重要性对其特征数值进行放缩的任务(feature recalibration)，RCAN中的RCAB实际上就是SE块的应用。
+ **Ftr:** Transformation模块，由卷积层组成，作用是提取和处理特征，经过这个模块得到的矩阵U： H * W * C。
> 卷积层使用的过滤器（filter）表示为V=[v1,v2,v3,...,vC], 其中C是filter的个数，vc表示第c个filter的参数。卷积层的输出表示为U=[u1,u2,u3,...,uC], uc由vc和输入X相乘计算得来。vc和X相乘实际上是vc的每个维和其对应的输入通道值相乘，最后对所有通道的结果求和。因为求和操作，可以看作Ftr隐式地考虑了通道之间的相互关系。但是这种方式不灵活，作者进一步设计了如何显示地考虑通道间的相互关系。
+ **Fsq：** squeeze模块，完成全局信息嵌入。特征图的单个数值元素受到**感受野（receptive field)** 大小的限制，无法描述全局信息，基于该数值的计算也只能表示其感受野内的特征。而Fsq想要对每一个通道都产生一个可以描述**全局**空间信息的**channel descriptor**, 描述器可以代表对应通道的重要性，所以这里使用了global average pooling作为Fsq。
> 全局平均池化对每一个通道计算一个该通道上所有特征值的平均值。Fsq可以有其它选择，这里使用了虽简单的方法。
+ **Fex：** Excitation模块。对Ftr提取到的特征进行自适应性缩放，这一部分也对应了RCAN中的**channel attention**。通过Fsq已经获得了对每个通道重要性的描述，但是这个描述只是使用平均池化得到的，不是学习得来的，而且没有显示地考虑通道之间的相互关系。这些问题由Fex解决。因此在设计Fex时，SE遵循两个设计原则，RCAN也借用了Fex的设计原则。
> 首先，Fex必须足够灵活，它需要能够学习到通道之间的非线性关系。  
> 其次，学习到的关系需要是非互斥的，即允许多个通道的重要性被强调。  

为了满足以上两个条件，SE使用了一个简单的sigmoid激活的门机制。
<img src=https://github.com/Yuehan717/Video-Super-resolution/blob/main/image/Fex-eq.png width=50%> </img>  
**delta：** ReLU函数，引入非线性因素  
**W1：** 全连接层（FC），比例为r的降维层。  
**W2：** 全连接层，比例为r的升维层。  
**sigma：** Sigmoid函数。
> 这里的门机制参考LSTM里的门控机制，在最外层使用了sigmoid函数。ReLU函数使得模块可以学习非线性关系。对于两个全连接层，论文里解释是在非线性函数两端形成bottle neck，我还没有理解这句话。个人理解是为了显式考虑相互关系，才使用了这种降维后再升维的方法。RCAN里把这两个全连接层替换为两个卷积层。

Fex处理得到的向量用于对每个通道进行缩放，这也是SE块的最后一步。
## 代码&实验
## 对比
